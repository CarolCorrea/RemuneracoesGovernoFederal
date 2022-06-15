# FolhaSalarialGovernoFederal
Projeto SSIS para baixar os Dados de Remuneração dos Servidores, Militares, Aposentados e Pensionistas do Governo Federal, disponíveis no Portal da Transparência, e gravar em Banco de Dados SQL Server, para uso em análises de dados no Power BI.

Link Projeto PowerBI: https://app.powerbi.com/view?r=eyJrIjoiMWU2MmZkNjgtNzIyNy00M2UyLThhMmYtMmU2MzYzNWZkZmE0IiwidCI6IjFkOTUyMTVlLTI4Y2EtNDlmMi04NTE3LTU1Mjc5ODc5ZTE5YSJ9

<br />

## Pré-requisitos
Instalação do Visual Studio, SQL Server e Power BI.

<br />

## Portal da Transparência
O Portal da Transparência do Governo Federal é um site de acesso livre, no qual o cidadão pode encontrar informações sobre como o dinheiro público é utilizado, além de se informar sobre assuntos relacionados à gestão pública do Brasil.
Na página de Downloads é possível consultar os dados disponíveis de acordo com o tema e a periodicidade de atualização, além de baixa-los em formato CSV, sem pré-requisitos para utilização.
Nesse projeto utilizamos os Dados de Servidores (Ativos e Inativos) e Pensionistas, os quais possuem atualização mensal.

Link: https://www.portaltransparencia.gov.br/download-de-dados/

<br />

## Orientações sobre o Projeto SSIS
O projeto é composto por 5 pacotes SSIS.

Pacote Importar Dados
- Faz download dos dados do Portal da Transparência, disponibilizados em arquivos CSV compactados (zip). Descompacta os arquivos e os salva em diretório local.
- Variáveis:
	- classificacao (Classificação dos dados que serão baixados, deve ser preenchida manualmente com "Servidores", "Militares", "Aposentados", "Pensionistas" ou "Honorarios")
	- dataAtual (Calcula a Data Atual - 1 mês. Esta é a data final dos dados que serão baixados. É atualizada automaticamente, portanto não precisa de alteração manual)
	- diretorioDados (Diretório local onde os arquivos serão salvos. Deve ser alterada de acordo com seu diretório local)
	- qtdMeses (Quantidade de meses de dados que serão baixados. Está definida como 6, mas caso desejar buscar mais meses, o número pode ser aumentado)
	- url (Url do Portal da Transparência, de onde os dados serão baixados. Precisa ser atualizada, caso haja alteração pelo Portão da Transparência)

Pacote Dimensões Stage: 
- Busca as Tabelas Dimensões (Aposentados, Militares, Pensionistas e Servidores) a partir de arquivos CSV e adiciona no Banco de dados Stage. 
- Variáveis: 
	- classificacao (Classificação dos dados que serão buscados, é atualizada automaticamente com "Servidores", "Militares", "Aposentados", "Pensionistas", portanto não precisa de alteração manual)
	- csvAposentados (Link dos arquivos CSV que contém os dados dos Aposentados. É atualizada automaticamente, portanto não precisa de alteração manual)
	- csvMilitares (Link dos arquivos CSV que contém os dados dos Militares. É atualizada automaticamente, portanto não precisa de alteração manual)
	- csvPensionistas (Link dos arquivos CSV que contém os dados dos Pensionistas. É atualizada automaticamente, portanto não precisa de alteração manual)
	- csvServidores (Link dos arquivos CSV que contém os dados dos Servidores. É atualizada automaticamente, portanto não precisa de alteração manual)
	- diretorioDados (Diretório local onde os arquivos serão salvos. Deve ser alterada de acordo com seu diretório local)

Pacote Dimensões DW:
- Busca as Tabelas Dimensões (Aposentados, Militares, Pensionistas e Servidores, Órgão e Órgão Superior) no Banco de Dados Stage, através de consultas SQL, executa algumas tarefas para preparar os dados para análise no Power BI, e replica para o Banco de Dados DW.

Pacote Fatos Stage: 
- Busca as Tabelas Fatos (Remunerações, Honorários Advocatícios e Honorários Jetons) a partir de arquivos CSV e adiciona no Banco de dados Stage. 
- Variáveis: 
	- classificacao (Classificação dos dados que serão buscados, é atualizada automaticamente com "Servidores", "Militares", "Aposentados", "Pensionistas", portanto não precisa de alteração manual)
	- csvHonorarios (Link dos arquivos CSV que contém os dados dos Honorários. É atualizada automaticamente, portanto não precisa de alteração manual)
	- csvRemuneracao (Link dos arquivos CSV que contém os dados das Remunerações. É atualizada automaticamente, portanto não precisa de alteração manual)
	- diretorioDados (Diretório local onde os arquivos serão salvos. Deve ser alterada de acordo com seu diretório local)
	- n (Variável utilizada internamente no Loop For, para que seja possível buscar os dados de remuneração de todas as classificações. É atualizada automaticamente, portanto não precisa de alteração manual)

Pacote Fatos DW:
- Busca as Tabelas Dimensões (Remunerações, Honorários Advocatícios e Honorários Jetons) no Banco de Dados Stage, executa algumas tarefas para preparar os dados para análise no Power BI, e replica para o Banco de Dados DW.
