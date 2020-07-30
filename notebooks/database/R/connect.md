Coleta de Dados no MySQL utilizando o R
================

## Setup

``` r
if(!"RMariaDB" %in% (.packages())){require(RMariaDB)}
```

    ## Loading required package: RMariaDB

``` r
if(!"xlsx" %in% (.packages())){require(xlsx)}
```

    ## Loading required package: xlsx

## Carregando as tabelas

``` r
con <- dbConnect(MariaDB(), user = 'root', password = '', dbname = 'bootcamp', host = 'localhost', serverTimezone = 'UTC')
tables <- dbListTables(con)
tables
```

    ## [1] "caracteristicageralimovel" "caracteristicasgerais"    
    ## [3] "cidade"                    "estado"                   
    ## [5] "imovel"                    "tipounidade"

## Listando dados de uma tabela

``` r
estados <- dbReadTable(con, 'estado')
colnames(estados)
```

    ## [1] "CodEstadoIBGE" "NomeEstado"    "SiglaEstado"   "Regiao"

``` r
cidades <- dbReadTable(con, 'cidade')
colnames(cidades)
```

    ## [1] "CodigoCompletoIBGE" "CodigoCidadeIBGE"   "NomeCidade"        
    ## [4] "CodEstadoIBGE"

``` r
imoveis <- dbReadTable(con, 'imovel')
colnames(imoveis)
```

    ##  [1] "idImovel"           "codRegistro"        "Disponibilidade"   
    ##  [4] "idTipoUnidade"      "areaImovel"         "valorIPTU"         
    ##  [7] "valorCondominio"    "qtdeQuartos"        "qtdeBanheiro"      
    ## [10] "qtdeSuite"          "qtdeSala"           "qtdeVagas"         
    ## [13] "CodigoCompletoIBGE"

``` r
unidades <- dbReadTable(con, 'tipounidade')
unidades
```

    ##   idTipoUnidade              dscTipoUnidade
    ## 1             1                        Casa
    ## 2             2               Casa geminada
    ## 3             3            Apartamento tipo
    ## 4             4       Apartamento cobertura
    ## 5             5 Apartamento Ã¡rea privativa
    ## 6             6                        Flat

## Criando uma Query direto no banco de dados

``` r
query <- "INSERT INTO tipounidade(idTipoUnidade, dscTipoUnidade) VALUES(7, 'Loft');"

results <- dbSendQuery(con, query)
results
```

    ## <MariaDBResult>
    ##   SQL  INSERT INTO tipounidade(idTipoUnidade, dscTipoUnidade) VALUES(7, 'Loft');
    ##   ROWS Fetched: 0 [complete]
    ##        Changed: 1

``` r
dbClearResult(results)
```

### Método alternativo

``` r
id <- 8
description <- 'Chácara'

query <- paste("INSERT INTO tipounidade(idTipoUnidade, dscTipoUnidade) VALUES(",id,", '",description,"');", sep='')

# Enviar resultado e retornar resposta
results <- dbSendQuery(con, query)
results
```

    ## <MariaDBResult>
    ##   SQL  INSERT INTO tipounidade(idTipoUnidade, dscTipoUnidade) VALUES(8, 'Chácara');
    ##   ROWS Fetched: 0 [complete]
    ##        Changed: 1

``` r
dbClearResult(results)
```

``` r
unidades <- dbReadTable(con, 'tipounidade')
unidades
```

    ##   idTipoUnidade              dscTipoUnidade
    ## 1             1                        Casa
    ## 2             2               Casa geminada
    ## 3             3            Apartamento tipo
    ## 4             4       Apartamento cobertura
    ## 5             5 Apartamento Ã¡rea privativa
    ## 6             6                        Flat
    ## 7             7                        Loft
    ## 8             8                     Chácara

## Escrevendo na tabela

``` r
path <- './datasets/'
filename <- paste(path, "estados.xlsx",sep='')
insertdata <- read.xlsx(filename, sheetIndex = 1, header = TRUE, encoding = 'UTF-8')
insertdata
```

    ##    CodEstadoIBGE          nomeestado siglaestado       regiao
    ## 1             11            Rondónia          RO        Norte
    ## 2             12                Acre          AC        Norte
    ## 3             13            Amazonas          AM        Norte
    ## 4             14             Roraima           R        Norte
    ## 5             15                Pará          PA        Norte
    ## 6             16               Amapá          AP        Norte
    ## 7             17           Tocantins          TO        Norte
    ## 8             21            Maranhão          MA     Nordeste
    ## 9             22               Piauí          PI     Nordeste
    ## 10            23               Ceará          CE     Nordeste
    ## 11            24 Rio Grande do Norte          RN     Nordeste
    ## 12            25             Paraíba          PB     Nordeste
    ## 13            26          Pernambuco          PE     Nordeste
    ## 14            27             Alagoas          AL     Nordeste
    ## 15            28             Sergipe          SE     Nordeste
    ## 16            29               Bahia          BA     Nordeste
    ## 17            31        Minas Gerais          MG      Sudeste
    ## 18            32      Espírito Santo          ES      Sudeste
    ## 19            33      Rio de Janeiro          RJ      Sudeste
    ## 20            34           São Paulo          SP      Sudeste
    ## 21            41              Paraná          PR          Sul
    ## 22            42      Santa Catarina          SC          Sul
    ## 23            43   Rio Grande do Sul          RS          Sul
    ## 24            50  Mato Grosso do Sul          MS Centro-Oeste
    ## 25            51         Mato Grosso          MT Centro-Oeste
    ## 26            52               Goiás          GO Centro-Oeste
    ## 27            53    Distrito Federal          DF Centro-Oeste

``` r
dbWriteTable(con, 'estado', insertdata, append = TRUE)
estados <- dbReadTable(con, 'estado')
estados
```

    ##    CodEstadoIBGE          NomeEstado SiglaEstado       Regiao
    ## 1             11            Rondónia          RO        Norte
    ## 2             12                Acre          AC        Norte
    ## 3             13            Amazonas          AM        Norte
    ## 4             14             Roraima           R        Norte
    ## 5             15                Pará          PA        Norte
    ## 6             16               Amapá          AP        Norte
    ## 7             17           Tocantins          TO        Norte
    ## 8             21            Maranhão          MA     Nordeste
    ## 9             22               Piauí          PI     Nordeste
    ## 10            23               Ceará          CE     Nordeste
    ## 11            24 Rio Grande do Norte          RN     Nordeste
    ## 12            25             Paraíba          PB     Nordeste
    ## 13            26          Pernambuco          PE     Nordeste
    ## 14            27             Alagoas          AL     Nordeste
    ## 15            28             Sergipe          SE     Nordeste
    ## 16            29               Bahia          BA     Nordeste
    ## 17            31        Minas Gerais          MG      Sudeste
    ## 18            32      Espírito Santo          ES      Sudeste
    ## 19            33      Rio de Janeiro          RJ      Sudeste
    ## 20            34           São Paulo          SP      Sudeste
    ## 21            41              Paraná          PR          Sul
    ## 22            42      Santa Catarina          SC          Sul
    ## 23            43   Rio Grande do Sul          RS          Sul
    ## 24            50  Mato Grosso do Sul          MS Centro-Oeste
    ## 25            51         Mato Grosso          MT Centro-Oeste
    ## 26            52               Goiás          GO Centro-Oeste
    ## 27            53    Distrito Federal          DF Centro-Oeste

``` r
query <- "SELECT * FROM estado;"

# Retorna apenas resultados
results <- dbGetQuery(con, query)
results
```

    ##    CodEstadoIBGE          NomeEstado SiglaEstado       Regiao
    ## 1             11            Rondónia          RO        Norte
    ## 2             12                Acre          AC        Norte
    ## 3             13            Amazonas          AM        Norte
    ## 4             14             Roraima           R        Norte
    ## 5             15                Pará          PA        Norte
    ## 6             16               Amapá          AP        Norte
    ## 7             17           Tocantins          TO        Norte
    ## 8             21            Maranhão          MA     Nordeste
    ## 9             22               Piauí          PI     Nordeste
    ## 10            23               Ceará          CE     Nordeste
    ## 11            24 Rio Grande do Norte          RN     Nordeste
    ## 12            25             Paraíba          PB     Nordeste
    ## 13            26          Pernambuco          PE     Nordeste
    ## 14            27             Alagoas          AL     Nordeste
    ## 15            28             Sergipe          SE     Nordeste
    ## 16            29               Bahia          BA     Nordeste
    ## 17            31        Minas Gerais          MG      Sudeste
    ## 18            32      Espírito Santo          ES      Sudeste
    ## 19            33      Rio de Janeiro          RJ      Sudeste
    ## 20            34           São Paulo          SP      Sudeste
    ## 21            41              Paraná          PR          Sul
    ## 22            42      Santa Catarina          SC          Sul
    ## 23            43   Rio Grande do Sul          RS          Sul
    ## 24            50  Mato Grosso do Sul          MS Centro-Oeste
    ## 25            51         Mato Grosso          MT Centro-Oeste
    ## 26            52               Goiás          GO Centro-Oeste
    ## 27            53    Distrito Federal          DF Centro-Oeste

## Exportar CSV

``` r
write.csv(results, './datasets/estadosDB.csv', row.names = FALSE, quote = FALSE)
```

## Escrever uma tabela vazia

``` r
query <- "CREATE TABLE caracteristicasgerais_1(
    idcaracteristicasGerais int NOT NULL AUTO_INCREMENT,
    dsccaracteristicasGerais varchar(150) NOT NULL,
    PRIMARY KEY (idcaracteristicasGerais));"

results <- dbSendQuery(con, query)
results
```

    ## <MariaDBResult>
    ##   SQL  CREATE TABLE caracteristicasgerais_1(
    ##     idcaracteristicasGerais int NOT NULL AUTO_INCREMENT,
    ##     dsccaracteristicasGerais varchar(150) NOT NULL,
    ##     PRIMARY KEY (idcaracteristicasGerais));
    ##   ROWS Fetched: 0 [complete]
    ##        Changed: 0

``` r
tables <- dbListTables(con)
```

    ## Warning in result_create(conn@ptr, statement, is_statement): Cancelling previous
    ## query

``` r
tables
```

    ## [1] "caracteristicageralimovel" "caracteristicasgerais"    
    ## [3] "caracteristicasgerais_1"   "cidade"                   
    ## [5] "estado"                    "imovel"                   
    ## [7] "tipounidade"

## Desconectar do Banco de Dados

``` r
dbDisconnect(con)
```
