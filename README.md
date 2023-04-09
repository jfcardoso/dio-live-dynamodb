# Módulo 'Boas Práticas com DynamoDB' do Bootcamp Banco PAN 2023

### Serviço utilizado
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando

### Comandos para execução do experimento:


- Criar uma tabela

```
aws dynamodb create-table \
    --table-name Discografia \
    --attribute-definitions \
        AttributeName=Artista,AttributeType=S \
        AttributeName=Título_da_Música,AttributeType=S \
    --key-schema \
        AttributeName=Artista,KeyType=HASH \
        AttributeName=Título_da_Música,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

- Inserir um item

```
aws dynamodb put-item \
    --table-name Discografia \
    --item file://itemmusic.json \
```

- Inserir múltiplos itens

```
aws dynamodb batch-write-item \
    --request-items file://batchmusic.json
```

- Criar um index global secundário baeado no título do álbum

```
aws dynamodb update-table \
    --table-name Discografia \
    --attribute-definitions AttributeName=Titulo_do_Album,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"Titulo_do_Album-index\",\"KeySchema\":[{\"AttributeName\":\"Titulo_do_Album\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no nome do artista e no título do álbum

```
aws dynamodb update-table \
    --table-name Discografia \
    --attribute-definitions\
        AttributeName=Artista,AttributeType=S \
        AttributeName=Titulo_do_Album,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artista\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"Titulo_do_Album\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no título da música e no ano

```
aws dynamodb update-table \
    --table-name Discografia \
    --attribute-definitions\
        AttributeName=Titulo_da_Musica,AttributeType=S \
        AttributeName=Lancamento,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"Titulo_da_Musica\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"Lancamento\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Pesquisar item por artista

```
aws dynamodb query \
    --table-name Discografia \
    --key-condition-expression "Artista = :artist" \
    --expression-attribute-values  '{":artista":{"S":"Prisma Brasil"}}'
```
- Pesquisar item por artista e título da música

```
aws dynamodb query \
    --table-name Discografia \
    --key-condition-expression "Artista = :artist and Titulo_da_Musica = :title" \
    --expression-attribute-values file://keyconditions.json
```

- Pesquisa pelo index secundário baseado no título do álbum

```
aws dynamodb query \
    --table-name Discografia \
    --index-name Titulo_do_Album-index \
    --key-condition-expression "Titulo_do_Album = :name" \
    --expression-attribute-values  '{":name":{"S":"Chamados por Cristo"}}'
```

- Pesquisa pelo index secundário baseado no nome do artista e no título do álbum

```
aws dynamodb query \
    --table-name Discografia \
    --index-name ArtistAlbumTitle-index \
    --key-condition-expression "Artista = :v_artist and Titulo_do_Album = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"Prisma Brasil"},":v_title":{"S":"Discípulo Teu"} }'
```

- Pesquisa pelo index secundário baseado no título da música e no ano

```
aws dynamodb query \
    --table-name Discografia \
    --index-name SongTitleYear-index \
    --key-condition-expression "Titulo_da_Musica = :v_song and Lancamento = :v_year" \
    --expression-attribute-values  '{":v_song":{"S":"Asas da Alva"},":v_year":{"S":"1988"} }'
```
