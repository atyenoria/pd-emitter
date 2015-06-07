require "json"
require "erb"
module Bootconf
  def load(file, init_var = {})
    begin
      json = JSON.load(open(file))
      @sensors = json["conf"]["sensors"]
      json
    rescue Errno::ENOENT
      File.write(file, init_var.to_json) # NOTE: 見つからなければ作成する (2015/5/11 MTG)
      # TODO: permission 660
      retry
    end
  end

  def erbfy(use_server)
    @sensors.map do |i|
      ERB.new(<<EOT).result(binding) if (i["use_servers"].include?(use_server) rescue false)
  <source>
    type unix_unimsg
    path /pd-emitter/v1/localname/<%= i["localname"].strip %>.sock
    use_abstract true
    key <%= @data_key %>
    tag <%= @routing_tag_prefix %>.<%= i["deid"].strip %>
  </source>
EOT
    end.join # TODO: #injectを使うべき？
  end

  def self.detect_format(file)
    "PD" # NOTE: ファイルを開き、フォーマットを特定して、Bootconf::FORMAT の FORMAT 文字を返す
  end
end

module Bootconf::PD
  extend Bootconf
  SERVER = "PD"
  INIT_VAR = {conf: {servers: {PD: {}}, sensors: []}}
  def self.inline_conf(file)
    json = load(file, INIT_VAR)
    server_conf = json["conf"]["servers"][SERVER]
    @data_key = server_conf["data_key"] || "__data__"
    ENV["PDEM_API_HOST"] = server_conf["url"] || "https://pd.plathome.com"
    ENV["PDEM_SECRET_KEY"] = server_conf["secretkey"] || ""
    ENV["PDEM_DATA_KEY"] = @data_key
    ENV["PDEM_TAG_KEY"] = server_conf["tag_key"] || "__tag__"
    @routing_tag_prefix = server_conf["routing_tag_prefix"] || "pd.pdex.v1.ob"
    ENV["PDEM_ROUTING_TAG_PREFIX"] = @routing_tag_prefix
    erbfy(SERVER)
  end
end

desc "start PD Emitter"
task :start do
  inline_conf = begin
                  ENV["CONF"].split.map do |bootconf_file| # NOTE: CONF="aa bb" で複数ファイルを渡せる
                    Bootconf.const_get(Bootconf.detect_format(bootconf_file)).inline_conf(bootconf_file)
                  end.join # TODO: #injectを使うべき？
                rescue NoMethodError # NOTE: undefined method `split' for nil:NilClass = 環境変数CONFが無い/空の場合の措置
                  ""
                end
  exec("bin/emitter", "-c", "conf/boot.conf", "--suppress-repeated-stacktrace", "--no-supervisor", "--emit-error-log-interval", "1", "--inline-config", inline_conf)
end

