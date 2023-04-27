
# docker for rails

# sqlite版

## 特徴
- 単なるrailsを起動するためのdocker-compose.ymlの整理
- dockerが必要


##  railsのインストール

- `docker-compose up -d` にてサーバ起動
- `docker-compose exec app bundle init` にて `Gemfile` を作成
- `docker-compose exec app bundle add rails` railsをシステムへインストール
- `docker-compose exec app rails new . -f`  railsを app へインストール
- `docker-compose exec app bundle install` で rails newで足りなかったgemをインストール
- `docker-compose exec app rails s -p 3000 -b 0.0.0.0` でサーバ起動
- http://localhost:3010/ にてアクセス
- 動いたのを確認したら docker-compose.ymlのcommandをコメントイン

## インストール後のサーバ起動

- `docker-compose up -d`
- http://localhost:3000/ にてアクセス

## サーバ停止

- `docker-compose down -v --remove-orphans`


# postgresql版

- 違いのみ記述する

## railsのインストール

- 最初の記述との違い
- `docker-compose up -d` → `docker-compose --profile postgres up`
- `docker-compose exec app rails new . -f -d postgresql`  railsを app へインストールのあと
- `ruby -e 'fn="config/database.yml"; s=open(fn).read.gsub(/(^default: &default$)/){"#{$1}\n  host: <%= ENV.fetch(\"DB_HOST\") %>\n  port: <%= ENV.fetch(\"DB_PORT\") %>\n  username: <%= ENV.fetch(\"DB_USER\") %>\n  password: <%= ENV.fetch(\"DB_PASSWORD\") %>"}; open(fn,"w").write(s)'` を実行

- 上のやつ DBPASSWORDとHOSTとか docker-composeから抜いてこないと駄目だな。
- とりあえず動かすだけだから環境変数を参照している意味がない

# mysql版

- 最初の記述との違い
- `docker-compose up -d` → `docker-compose --profile mysql up`
- `docker-compose exec app rails new . -f -d mysql`  railsを app へインストールのあと
- `ruby -e 'fn="config/database.yml"; s=open(fn).read.gsub(/(^default: &default$)/){"#{$1}\n  host: <%= ENV.fetch(\"DB_HOST\") %>\n  port: <%= ENV.fetch(\"DB_PORT\") %>\n  username: <%= ENV.fetch(\"DB_USER\") %>\n  password: <%= ENV.fetch(\"DB_PASSWORD\") %>"}; open(fn,"w").write(s)'` を実行


# apiサーバ版

- ほぼ最初と同じで `rails new` 時に `--api` をつけるだけ

## テスト

- Helloコントローラを作成

```
rails g controller Hello
ruby -e 'fn="app/controllers/hello_controller.rb";s=open(fn).read.gsub(/^end\Z/,"  def index; render json:{hello:\"world\"}; end;\nend"); open(fn,"w").write(s)'
ruby -e 'fn="config/routes.rb";s=open(fn).read.gsub(/^end\Z/,"  get \"hello\", to: \"hello#index\"\nend"); open(fn,"w").write(s)'
```

```
rails s -b 0.0.0.0
```

```
$ curl http://localhost:3010/hello
{"hello":"world"}
```
