<properties
	pageTitle="Supporto dei client di livello inferiore del database SQL e modifiche all'endpoint IP per Controllo | Microsoft Azure"
	description="Informazioni sul supporto dei client di livello inferiore del database SQL e modifiche all'endpoint IP per Controllo"
	services="sql-database"
	documentationCenter=""
	authors="nadavhelfman"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="nadavh;ronitr"/>

# Database SQL: supporto dei client di livello inferiore e modifiche all'endpoint IP per Controllo


[Controllo](sql-database-auditing-get-started.md) funziona automaticamente con i client SQL che supportano il reindirizzamento TDS.


##<a id="subheading-1"></a>Supporto dei client di livello inferiore

Qualsiasi client che implementa TDS 7.4 deve supportare anche il reindirizzamento. Rappresentano un'eccezione JDBC 4.0, in cui non è del tutto supportata la funzionalità di reindirizzamento, e Tedious per Node.JS, in cui non è implementato il reindirizzamento.

Per i "client di livello inferiore", ad esempio quelli che supportano la versione 7.3 di TDS e inferiori, il nome di dominio completo del server nella stringa di connessione deve essere modificato:

Nome di dominio completo del server originale nella stringa di connessione: <*server name*>.database.windows.net

Nome di dominio completo del server modificato nella stringa di connessione: <*server name*>.database.**secure**.windows.net

Un elenco parziale di "client di livello inferiore" include:

- .NET 4.0 e versioni precedenti,
- ODBC 10.0 e versioni precedenti.
- JDBC (mentre JDBC supporta TDS 7.4, la funzionalità di reindirizzamento TDS non è del tutto supportata)
- Tedious (per Node.JS)

**Nota:** la modifica del nome di dominio completo del server citata in precedenza può risultare utile per applicare un criterio di controllo a livello di server SQL senza la necessità di una procedura di configurazione in ogni database (attenuazione temporanea).

##<a id="subheading-2"></a>Modifiche all'endpoint IP quando si abilita il Controllo

Si noti che quando si abilita il Controllo, l'endpoint IP del database viene modificato. Se si dispone di impostazioni del firewall restrittive, aggiornarle.

Il nuovo endpoint IP del database dipenderà dall'area del database:

| Area del database | Endpoint IP possibili |
|----------|---------------|
| Cina settentrionale | 139\.217.29.176, 139.217.28.254 |
| Cina orientale | 42\.159.245.65, 42.159.246.245 |
| Australia orientale | 104\.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australia sudorientale | 191\.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brasile meridionale | 104\.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Stati Uniti centrali | 104\.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Asia orientale | 23\.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Stati Uniti orientali 2 | 104\.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Stati Uniti orientali | 23\.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| India centrale | 104\.211.98.219, 104.211.103.71 |
| India meridionale | 104\.211.227.102, 104.211.225.157 |
| India occidentale | 104\.211.161.152, 104.211.162.21 |
| Giappone orientale | 104\.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Giappone occidentale | 104\.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Stati Uniti centro-settentrionali | 191\.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europa settentrionale | 104\.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Stati Uniti centro-meridionali | 191\.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Asia sudorientale | 104\.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa occidentale | 104\.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Stati Uniti occidentali | 191\.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Canada centrale | 13\.88.248.106 |
| Canada orientale | 40\.86.227.82 |

<!---HONumber=AcomDC_0615_2016-->