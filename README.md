# Elasticsearch で IS NULL 的な検索をするための検証

Qiita の記事 → こちら

## 環境構築

### コンテナの準備

Docker コンテナ上での作業をしている  
個人的に使いやすいという理由で Kibana も使っている

```bash
docker-compose up -p
```

立ち上げ後のヘルスチェック

```
# Elasticsearch
curl -X GET "localhost:9200/_cat/health?v"
# Kibana
curl -l "localhost:5601/api/status" | jq
```

### データの作成

インデックスの定義は`index_definitions`ディレクトリに、投入するドキュメントは`es_documents`ディレクトリに入っている  
インデックスの作成・データの登録はそれぞれ以下の通り

#### インデックスの作成

```
PUT / インデックス名
(インデックス定義)
```

例  
`index_definitions/simple_index.json`を用いた場合

```json
PUT /simple_index
{
    "mappings":
    {
        "_routing": {"required": false},
	    "properties":
	    {
	    	"id": {"type": "keyword"},
	    	"name": {"type": "keyword"},
	    	"int_value": {"type": "integer"}
	    }
    }
}
```

#### ドキュメントの登録

```
POST / インデックス名/_bulk
(登録するドキュメントたち)
```

例

```json
POST /simple_index/_bulk
{"create":{"_index": "simple_index"}}
{ "id": "1","name": "sample_1","int_value": 10}
{"create":{"_index": "simple_index"}}
{ "id": "2","name": "sample_2","int_value": 20}
{"create":{"_index": "simple_index"}}
{ "id": "3","name": "sample_3","int_value": null}
...(以下略)
```
