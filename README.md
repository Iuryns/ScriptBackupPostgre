# ScriptBackupPostgre

Este Script é para otimizar o backupp, e antes de finalizar o script criar outro banco de dados e restaurar o backup para teste, quando for fazer o backup novamente excluirá o banco criado, assim dará tempo para verificar o banco teste, antes de encerrar o script irá compactar o arquivo de backup e excluirá o outro arquivo não compactado, ainda criará uma pasta de log para armazenar a data de todos os backups realizados.

#!/bin/bash
#Declarando variaveis

data=`date "+%Y%m%d-%H:%M"`
usuario=postgres
host=localhost
banco=postgre
senha=201420
restaurar=postgre
teste= bancodeTeste
#Conectando ao banco de dados

su - postgres
#Excluindo o bancoTeste criado para teste no primeiro backup

psql -c dropdb $teste
#Realizando Backup do banco de dados.

pg_dump -h localhost -p 5432 -U postgres -v -f "/backup/$data.dbserver.sql"
#Criando um banco teste e restaurando Backup do banco de dados.

psql -c createdb $teste

pg_restore -U $usuario -h $host -d  $teste /backup/$data.dbserver.sql
#Compactando o Backup do banco de dados

tar -zcf /backup/compactado/$data.dbserversql.tar /backup/$data.dbserver.sql
#Apagando arquivo sem compactacao do Backup

rm -rf /backup/$data.dbserver.sql
#Criando uma pasta de log para os backups feitos e acrescentando a data toda vez que o backup for feito

$data >>/var/log/backup.log
