---
title: Fusi orari Istanza gestita di database SQL di Azure | Microsoft Docs "
description: Informazioni sulle specifiche del fuso orario di Istanza gestita di database SQL di Azure
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: e81ae2fc563300402339fc40893fbbdbbd326dcd
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233231"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusi orari in Istanza gestita di database SQL di Azure

Coordinated Universal Time (UTC) è il fuso orario consigliato per il livello dati di soluzioni cloud. Istanza gestita di database SQL di Azure offre anche una scelta dei fusi orari per soddisfare le esigenze delle applicazioni esistenti che archiviano i valori di data e ora e chiamano funzioni di data e ora con un contesto implicito di un fuso orario specifico.

Le funzioni T-SQL come [GETDATE ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) o il codice CLR osservano il fuso orario impostato a livello di istanza. SQL Server Agent processi seguono anche le pianificazioni in base al fuso orario dell'istanza.

  >[!NOTE]
  > Istanza gestita è l'unica opzione di distribuzione del database SQL di Azure che supporta l'impostazione del fuso orario. Altre opzioni di distribuzione seguono sempre l'ora UTC.
Usare [at Time zone](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) nei database SQL singoli e in pool se è necessario interpretare le informazioni di data e ora in un fuso orario non UTC.

## <a name="supported-time-zones"></a>Fusi orari supportati

Un set di fusi orari supportati viene ereditato dal sistema operativo sottostante dell'istanza gestita. Viene regolarmente aggiornata per ottenere nuove definizioni di fuso orario e riflettere le modifiche apportate a quelle esistenti.

I [criteri per le modifiche dell'ora legale e del fuso orario](https://aka.ms/time) garantiscono l'accuratezza cronologica da 2010 in poi.

Un elenco con i nomi dei fusi orari supportati viene esposto tramite la vista di sistema [sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) .

## <a name="set-a-time-zone"></a>Impostare un fuso orario

Un fuso orario di un'istanza gestita può essere impostato solo durante la creazione dell'istanza. Il fuso orario predefinito è UTC.

  >[!NOTE]
  > Il fuso orario di un'istanza gestita esistente non può essere modificato.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Impostare il fuso orario tramite il portale di Azure

Quando si immettono i parametri per una nuova istanza, selezionare un fuso orario nell'elenco dei fusi orari supportati.
  
![Impostazione di un fuso orario durante la creazione dell'istanza](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Specificare la proprietà timezoneId nel [modello di gestione risorse](https://aka.ms/sql-mi-create-arm-posh) per impostare il fuso orario durante la creazione dell'istanza.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Alla fine di questo articolo è presente un elenco di valori supportati per la proprietà timezoneId.

Se non è specificato, il fuso orario è impostato su UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Controllare il fuso orario di un'istanza

La funzione [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) restituisce un nome visualizzato del fuso orario dell'istanza.

## <a name="cross-feature-considerations"></a>Considerazioni tra funzionalità

### <a name="restore-and-import"></a>Ripristino e importazione

È possibile ripristinare un file di backup o importare dati in un'istanza gestita da un'istanza di o da un server con diverse impostazioni di fuso orario. Assicurarsi di eseguire questa operazione con cautela. Analizzare il comportamento dell'applicazione e i risultati di query e report, proprio come quando si trasferiscono dati tra due istanze di SQL Server con impostazioni di fuso orario diverse.

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Quando si esegue un ripristino temporizzato, il tempo necessario per il ripristino viene interpretato come ora UTC. In questo modo vengono evitate le ambiguità dovute all'ora legale e alle relative modifiche potenziali.

### <a name="auto-failover-groups"></a>Gruppi di failover automatico

L'utilizzo dello stesso fuso orario in un'istanza primaria e secondaria in un gruppo di failover non viene applicato, ma è consigliabile.

  >[!WARNING]
  > Si consiglia vivamente di utilizzare lo stesso fuso orario per l'istanza primaria e secondaria in un gruppo di failover. A causa di alcuni rari casi d'uso, non è applicato lo stesso fuso orario tra le istanze primarie e secondarie. È importante comprendere che in caso di failover manuale o automatico, l'istanza secondaria manterrà il fuso orario originale.

## <a name="limitations"></a>Limitazioni

- Il fuso orario dell'istanza gestita esistente non può essere modificato.
- I processi esterni avviati dal SQL Server Agent processi non osservano il fuso orario dell'istanza.

## <a name="list-of-supported-time-zones"></a>Elenco di fusi orari supportati

| **ID fuso orario** | **Nome visualizzato del fuso orario** |
| --- | --- |
| Ora solare linea cambiamento data | (UTC-12.00) Linea cambiamento data internazionale (occidentale) |
| UTC-11 | (UTC-11.00) Coordinated Universal Time-11 |
| Ora solare Aleutine | (UTC-10.00) Isole Aleutine |
| Ora solare Hawaii | (UTC-10.00) Hawaii |
| Ora solare Marquesas | (UTC-09.30) Isole Marquesas |
| Ora solare Alaska | (UTC-09.00) Alaska |
| UTC-09 | (UTC-09.00) Coordinated Universal Time-09 |
| Ora solare Pacifico (Messico) | (UTC-08.00) Bassa California |
| UTC-08 | (UTC-08.00) Coordinated Universal Time-08 |
| Ora solare Pacifico | (UTC-08.00) Pacifico (USA e Canada) |
| Ora solare fuso occidentale Stati Uniti | (UTC-07.00) Arizona |
| Ora solare fuso occidentale (Messico) | (UTC-07.00) Chihuahua, La Paz, Mazatlan |
| Ora solare fuso occidentale | (UTC-07.00) Fuso occidentale (USA e Canada) |
| Ora solare America centrale | (UTC-06.00) America centrale |
| Ora solare fuso centrale | (UTC-06.00) Fuso centrale (USA e Canada) |
| Ora solare Isola di Pasqua | (UTC-06.00) Isola di Pasqua |
| Ora solare fuso centrale (Messico) | (UTC-06.00) Guadalajara, Città del Messico, Monterrey |
| Ora solare Canada centrale | (UTC-06.00) Saskatchewan |
| Ora solare America del Sud Pacifico | (UTC-05.00) Bogotá, Lima, Quito, Rio Branco |
| Ora solare fuso orientale (Messico) | (UTC-05.00) Chetumal |
| Ora solare fuso orientale | (UTC-05.00) Fuso orientale (USA e Canada) |
| Ora solare Haiti | (UTC-05.00) Haiti |
| Ora solare Cuba | (UTC-05.00) L'Avana |
| Ora solare Stati Uniti orientali | (UTC-05.00) Indiana (Est) |
| Ora solare Turks e Caicos | (UTC-05:00) Turks e Caicos |
| Ora solare Paraguay | (UTC-04.00) Asunción |
| Ora solare costa atlantica | (UTC-04.00) Ora costa atlantica (Canada) |
| Ora solare Venezuela | (UTC-04.00) Caracas |
| Ora solare Brasile centrale | (UTC-04.00) Cuiaba |
| Ora solare America del Sud occidentale | (UTC-04.00) Georgetown, La Paz, Manaus, San Juan |
| Ora solare America del Sud Pacifico | (UTC-04.00) Santiago |
| Ora solare Terranova | (UTC-03.30) Terranova |
| Ora solare Tocantins | (UTC-03.00) Araguaina |
| E. Ora solare America del sud | (UTC-03.00) Brasilia |
| Ora solare America del Sud orientale | (UTC-03.00) Caienna, Fortaleza |
| Ora solare Argentina | (UTC-03.00) Buenos Aires |
| Ora solare Groenlandia | (UTC-03.00) Groenlandia |
| Ora solare Montevideo | (UTC-03.00) Montevideo |
| Ora solare Magallanes | (UTC-03:00) Arene di punta |
| Ora solare Saint-Pierre | (UTC-03.00) Saint Pierre e Miquelon |
| Ora solare Bahia | (UTC-03.00) El Salvador |
| UTC-02 | (UTC-02.00) Coordinated Universal Time-02 |
| Ora solare metà Atlantico | (UTC-02.00) Medioatlantico - Vecchio |
| Ora solare Azzorre | (UTC-01.00) Azzorre |
| Ora solare Cabo verde | (UTC-01.00) Is. di Cabo Verde |
| UTC | (UTC) Coordinated Universal Time |
| Ora solare di Greenwich | (UTC+00.00) Dublino, Edimburgo, Lisbona, Londra |
| Ora solare di Greenwich | (UTC+00.00) Monrovia, Reykjavik |
| W. Ora solare Europa | (UTC+01.00) Amsterdam, Berlino, Berna, Roma, Stoccolma, Vienna |
| Ora solare Europa Centrale | (UTC+01.00) Belgrado, Bratislava, Budapest, Lubiana, Praga |
| Ora solare Bruxelles, Copenaghen, Madrid, Parigi | (UTC+01.00) Bruxelles, Copenaghen, Madrid, Parigi |
| Ora solare Marocco | (UTC + 01:00) Casablanca |
| Ora solare São Tomé | (UTC + 01:00) Sao Tome |
| Ora solare Europa Centrale | (UTC+01.00) Sarajevo, Skopje, Varsavia, Zagabria |
| W. Ora solare Africa centrale | (UTC+01.00) Africa centro-occidentale |
| Ora solare Giordania | (UTC+02.00) Amman |
| Ora solare GTB | (UTC+02.00) Atene, Bucarest |
| Ora solare Medio Oriente | (UTC+02.00) Beirut |
| Ora solare Egitto | (UTC+02.00) Cairo |
| E. Ora solare Europa | (UTC+02.00) Chisinau |
| Ora solare Siria | (UTC+02.00) Damasco |
| Ora solare Cisgiordania | (UTC+02.00) Hebron |
| Ora solare Sudafrica | (UTC+02.00) Harare, Pretoria |
| Ora solare FLE | (UTC+02.00) Helsinki, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Ora solare Israele | (UTC+02.00) Gerusalemme |
| Ora solare Kaliningrad | (UTC+02.00) Kaliningrad |
| Ora solare Sudan | (UTC + 2.00 H) Khartoum |
| Ora solare Libia | (UTC+02.00) Tripoli |
| Ora solare Namibia | (UTC + 2.00 H) Windhoek |
| Ora solare Arabia Saudita | (UTC+03.00) Baghdad |
| Ora solare Turchia | (UTC + 3.00 H) Istanbul |
| Ora solare Arabia | (UTC+03.00) Kuwait, Riyadh |
| Ora solare Bielorussia | (UTC+03.00) Minsk |
| Ora solare russo | (UTC + 3.00 H) Mosca, San Pietroburgo |
| E. Ora solare Africa | (UTC+03.00) Nairobi |
| Ora solare Iran | (UTC+03.30) Teheran |
| Ora solare Emirati Arabi Uniti | (UTC+04.00) Abu Dhabi, Mascate |
| Ora solare Astrakhan | (UTC+04.00) Astrakhan, Ulyanovsk |
| Ora solare Azerbaigian | (UTC+04.00) Baku |
| Tempo di Russia Zona 3 | (UTC+04.00) Izhevsk, Samara |
| Ora solare Mauritius | (UTC+04.00) Port Louis |
| Ora solare Saratov | (UTC + 4.00 H) Saratov |
| Ora solare Georgia | (UTC+04.00) Tbilisi |
| Ora solare Volgograd | (UTC + 4.00 H) Volgograd |
| Ora solare Caucaso | (UTC+04.00) Erevan |
| Ora solare Afghanistan | (UTC+04.30) Kabul |
| Ora solare Asia occidentale | (UTC+05.00) Ashgabat, Tashkent |
| Ora solare Ekaterinburg | (UTC+05.00) Ekaterinburg |
| Ora solare Pakistan | (UTC+05.00) Islamabad, Karachi |
| Ora solare India | (UTC+05.30) Chennai, Kolkata (Calcutta), Mumbai, Nuova Delhi |
| Ora solare Sri Lanka | (UTC+05.30) Sri Jayawardenepura |
| Ora solare Nepal | (UTC+05.45) Katmandu |
| Ora solare Asia centrale | (UTC+06.00) Astana |
| Ora solare Bangladesh | (UTC+06.00) Dacca |
| Ora solare Omsk | (UTC + 6.00 H) Omsk |
| Ora solare Myanmar | (UTC+06.30) Yangon (Rangoon) |
| Ora solare Asia sud-orientale | (UTC+07.00) Bangkok, Hanoi, Giacarta |
| Ora solare Altai | (UTC+07.00) Barnaul, Gorno-Altaysk |
| W. Ora solare Mongolia | (UTC+07.00) Hovd |
| Ora solare Asia settentrionale | (UTC+07.00) Krasnoyarsk |
| N. Ora solare Asia centrale | (UTC + 07:00) Novosibirsk |
| Ora solare Tomsk | (UTC+07.00) Tomsk |
| Ora solare Cina | (UTC+08:00) Beijing (Pechino), Chongqing, Hong Kong - R.A.S., Urumqi |
| Ora solare Nord Asia orientale | (UTC+08.00) Irkutsk |
| Ora solare Singapore | (UTC+08.00) Kuala Lumpur, Singapore |
| W. Ora solare Australia | (UTC+08.00) Perth |
| Ora solare Taipei | (UTC+08.00) Taipei |
| Ora solare Ulan-Bator | (UTC+08.00) Ulan-Bator |
| Ora solare centrale aus | (UTC+08.45) Eucla |
| Ora solare Transbaikal | (UTC+09.00) Chita |
| Ora solare Tokyo | (UTC+09.00) Osaka, Sapporo, Tokyo |
| Ora solare Corea del Nord | (UTC + 09:00) Pyongyang |
| Ora solare Corea | (UTC+09.00) Seoul |
| Ora solare Yakutsk | (UTC+09.00) Yakutsk |
| CEN. Ora solare Australia | (UTC+09.30) Adelaide |
| Ora solare Australia centrale | (UTC+09.30) Darwin |
| E. Ora solare Australia | (UTC+10.00) Brisbane |
| Ora solare Australia orientale | (UTC+10.00) Canberra, Melbourne, Sydney |
| Ora solare Pacifico occidentale | (UTC+10.00) Guam, Port Moresby |
| Ora solare Tasmania | (UTC+10.00) Hobart |
| Ora solare Vladivostok | (UTC+10.00) Vladivostok |
| Ora solare Lord Howe | (UTC+10.30) Isola Lord Howe |
| Ora solare Bougainville | (UTC+11.00) Bougainville |
| Fuso orario Russia 10 | (UTC+11.00) Chokurdakh |
| Ora solare Magadan | (UTC+11.00) Magadan |
| Ora solare Norfolk | (UTC+11.00) Norfolk |
| Ora solare Sakhalin | (UTC+11.00) Sakhalin |
| Ora solare Pacifico centrale | (UTC+11.00) Is. Salomone, Nuova Caledonia |
| Fuso orario Russia 11 | (UTC+12.00) Anadyr, Petropavlovsk-Kamčatskij |
| Ora solare Nuova Zelanda | (UTC+12.00) Auckland, Wellington |
| UTC + 12 | (UTC+12.00) Coordinated Universal Time+12 |
| Ora solare Figi | (UTC+12.00) Figi |
| Ora solare Kamchatka | (UTC+12.00) Petropavlovsk-Kamčatskij - Vecchio |
| Ora solare Isole Chatham | (UTC+12.45) Isole Chatham |
| UTC + 13 | (UTC + 13.00) Ora UTC (Coordinated Universal Time) + 13 |
| Ora solare Tonga | (UTC+13.00) Nuku'alofa |
| Ora solare Samoa | (UTC+13.00) Samoa |
| Ora solare Isole Line | (UTC+14.00) Isola di Kiritimati |

## <a name="see-also"></a>Vedere anche 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
