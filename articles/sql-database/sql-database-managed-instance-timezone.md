---
title: Fusi orari Istanza gestita
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
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256094"
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

La funzione [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) restituisce un nome visualizzato del fuso orario dell'istanza di.

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
| Ora solare Dateline | (UTC-12:00) Linea data internazionale ovest |
| UTC-11 | (UTC-11:00) Ora UTC (Coordinated Universal Time)-11 |
| Ora solare Aleutine | (UTC-10:00) Isole Aleutine |
| Ora solare Hawaii | (UTC-10:00) Hawaii |
| Ora solare Marquess | (UTC-09:30) Isole Marques |
| Ora solare Alaska | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Ora UTC (Coordinated Universal Time-09) |
| Ora solare Pacifico (Messico) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Coordinated Universal Time-08 |
| Ora solare Pacifico | (UTC-08:00) Pacifico (Stati Uniti & Canada) |
| Ora solare Stati Uniti della montagna | (UTC-07:00) Arizona |
| Ora solare fuso occidentale (Messico) | (UTC-07:00) Chihuahua, la Paz, Mazatlan |
| Ora solare fuso occidentale | (UTC-07:00) Mountain Time (Stati Uniti & Canada) |
| Ora solare America centrale | (UTC-06:00) America centrale |
| Ora solare fuso centrale | (UTC-06:00) Fuso orario centrale (US & Canada) |
| Ora solare isola di Pasqua | (UTC-06:00) Isola di Pasqua |
| Ora solare fuso centrale (Messico) | (UTC-06:00) Guadalajara, città del Messico, Monterrey |
| Ora solare Canada centrale | (UTC-06:00) Saskatchewan |
| Ora solare SA Pacifico | (UTC-05:00) Bogotà, Lima, Quito, Rio Branco |
| Ora solare fuso orientale (Messico) | (UTC-05:00) Chetumal |
| Ora solare fuso orientale | (UTC-05:00) Fuso orientale (USA & Canada) |
| Ora solare Haiti | (UTC-05:00) Haiti |
| Ora solare Cuba | (UTC-05:00) L'Avana |
| Ora solare Stati Uniti orientali | (UTC-05:00) Indiana (est) |
| Ora solare Turks e Caicos | (UTC-05:00) Turks e Caicos |
| Ora solare Paraguay | (UTC-04:00) Asuncion |
| Ora solare Atlantico | (UTC-04:00) Fuso costa atlantica (Canada) |
| Ora solare Venezuela | (UTC-04:00) Caracas |
| Ora solare Brasile centrale | (UTC-04:00) Cuiaba |
| Ora solare Europa occidentale | (UTC-04:00) Georgetown, la Paz, Manaus, San Juan |
| Ora solare Pacific SA | (UTC-04:00) Santiago |
| Ora solare Terranova | (UTC-03:30) Terranova |
| Ora solare Tocantins | (UTC-03:00) Araguaina |
| E. Ora solare America del sud | (UTC-03:00) Brasilia |
| Ora solare SA Eastern | (UTC-03:00) Caienna, Fortaleza |
| Ora solare Argentina | (UTC-03:00) Città di Buenos Aires |
| Ora solare Groenlandia | (UTC-03:00) Groenlandia |
| Ora solare Montevideo | (UTC-03:00) Montevideo |
| Ora solare Masini | (UTC-03:00) Arene di punta |
| Ora solare Saint-Pierre | (UTC-03:00) Saint-Pierre e Miquelon |
| Ora solare Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Ora UTC (Coordinated Universal Time)-02 |
| Ora solare metà Atlantico | (UTC-02:00) Mid-Atlantic-vecchio |
| Ora solare Azzorre | (UTC-01:00) Azzorre |
| Ora solare Cabo verde | (UTC-01:00) Cabo verde è. |
| UTC | (UTC) Coordinated Universal Time |
| Ora solare GMT | (UTC + 00:00) Dublino, Edimburgo, Lisbona, Londra |
| Ora solare Greenwich | (UTC + 00:00) Monrovia, Reykjavik |
| W. Ora solare Europa | (UTC + 01:00) Amsterdam, Berlino, Berna, Roma, Stoccolma, Vienna |
| Ora solare Europa centrale | (UTC + 01:00) Belgrado, Bratislava, Budapest, Lubiana, Praga |
| Ora solare Romance | (UTC + 01:00) Bruxelles, Copenaghen, Madrid, Parigi |
| Ora solare Marocco | (UTC + 01:00) Casablanca |
| Ora solare Sao Tome | (UTC + 01:00) Sao Tome |
| Ora solare Europa centrale | (UTC + 01:00) Sarajevo, Skopje, Varsavia, Zagabria |
| W. Ora solare Africa centrale | (UTC + 01:00) Africa centro-occidentale |
| Ora solare Giordania | (UTC + 2.00 H) Amman |
| Ora solare GTB | (UTC + 2.00 H) Atene, Bucarest |
| Ora solare medio Oriente | (UTC + 2.00 H) Beirut |
| Ora solare Egitto | (UTC + 2.00 H) Cairo |
| E. Ora solare Europa | (UTC + 2.00 H) Chişinău |
| Ora solare Siria | (UTC + 2.00 H) Damasco |
| Ora solare Cisgiordania | (UTC + 2.00 H) Gaza, Hebron |
| Ora solare Sudafrica | (UTC + 2.00 H) Harare, Pretoria |
| Ora solare ora solare FLE | (UTC + 2.00 H) Helsinki, Kiev, riga, Sofia, Tallinn, Vilnius |
| Ora solare Israele | (UTC + 2.00 H) Gerusalemme |
| Ora solare Kaliningrad | (UTC + 2.00 H) Kaliningrad |
| Ora solare Sudan | (UTC + 2.00 H) Khartoum |
| Ora solare Libia | (UTC + 2.00 H) Tripoli |
| Ora solare Namibia | (UTC + 2.00 H) Windhoek |
| Ora solare arabo | (UTC + 3.00 H) Baghdad |
| Ora solare Turchia | (UTC + 3.00 H) Istanbul |
| Ora solare arabo | (UTC + 3.00 H) Kuwait, Riyadh |
| Ora solare Bielorussia | (UTC + 3.00 H) Minsk |
| Ora solare russo | (UTC + 3.00 H) Mosca, San Pietroburgo |
| E. Ora solare Africa | (UTC + 3.00 H) Nairobi |
| Ora solare Iran | (UTC + 03:30) Teheran |
| Ora solare Arabia Saudita | (UTC + 4.00 H) Abu Dhabi, Muscat |
| Ora solare Astrakhan | (UTC + 4.00 H) Astrakhan, Ulyanovsk |
| Ora solare Azerbaigian | (UTC + 4.00 H) Baku |
| Tempo di Russia Zona 3 | (UTC + 4.00 H) Izhevsk, Samara |
| Ora solare Mauritius | (UTC + 4.00 H) Porta Louis |
| Ora solare Saratov | (UTC + 4.00 H) Saratov |
| Ora solare georgiano | (UTC + 4.00 H) Tbilisi |
| Ora solare Volgograd | (UTC + 4.00 H) Volgograd |
| Ora solare Caucaso | (UTC + 4.00 H) Yerevan |
| Ora solare Afghanistan | (UTC + 4.00:30) Kabul |
| Ora solare Asia occidentale | (UTC + 05:00) Ashkhabat, Tashkent |
| Ora solare Ekaterinburg | (UTC + 05:00) Ekaterinburg |
| Ora solare Pakistan | (UTC + 05:00) Islamabad, Karachi |
| Ora solare India | (UTC + 05:30) Chennai, Calcutta, Mumbai, Nuova Delhi |
| Ora solare Sri Lanka | (UTC + 05:30) Sri Jayawardenepura |
| Ora solare Nepal | (UTC + 05:45) Katmandu |
| Ora solare Asia centrale | (UTC + 6.00 H) Astana |
| Ora solare Bangladesh | (UTC + 6.00 H) Dacca |
| Ora solare Omsk | (UTC + 6.00 H) Omsk |
| Ora solare Myanmar | (UTC + 06:30) Yangon (Rangoon) |
| Ora solare Asia sud-est | (UTC + 07:00) Bangkok, Hanoi, Giacarta |
| Ora solare Altai | (UTC + 07:00) Barnaul, Gorno-Altaysk |
| W. Ora solare Mongolia | (UTC + 07:00) Hovd |
| Ora solare Asia settentrionale | (UTC + 07:00) Krasnoyarsk |
| N. Ora solare Asia centrale | (UTC + 07:00) Novosibirsk |
| Ora solare Tomsk | (UTC + 07:00) Tomsk |
| Ora solare Cina | (UTC + 08:00) Pechino, Chongqing, Hong Kong, Urumqi |
| Ora solare Nord Asia orientale | (UTC + 08:00) Irkutsk |
| Ora solare Singapore | (UTC + 08:00) Kuala Lumpur, Singapore |
| W. Ora solare Australia | (UTC + 08:00) Perth |
| Ora solare Taipei | (UTC + 08:00) Taipei |
| Ora solare Ulaanbaatar | (UTC + 08:00) Ulaanbaatar |
| Ora solare centrale aus | (UTC + 08:45) Eucla |
| Ora solare Transbaikal | (UTC + 09:00) Chita |
| Ora solare Tokyo | (UTC + 09:00) Osaka, Sapporo, Tokyo |
| Ora solare Corea del Nord | (UTC + 09:00) Pyongyang |
| Ora solare Corea | (UTC + 09:00) Seoul |
| Ora solare Yakutsk | (UTC + 09:00) Yakutsk |
| CEN. Ora solare Australia | (UTC + 09:30) Adelaide |
| Ora solare centrale AUS | (UTC + 09:30) Darwin |
| E. Ora solare Australia | (UTC + 10.00 H) Brisbane |
| Ora solare Australia orientale | (UTC + 10.00 H) Canberra, Melbourne, Sydney |
| Ora solare Pacifico occidentale | (UTC + 10.00 H) Guam, Port Moresby |
| Ora solare Tasmania | (UTC + 10.00 H) Hobart |
| Ora solare Vladivostok | (UTC + 10.00 H) Vladivostok |
| Ora solare Lord Howe | (UTC + 10:30) Isola di Lord Howe |
| Ora solare Bougainville | (UTC + 11.00) Isola Bougainville |
| Fuso orario Russia 10 | (UTC + 11.00) Čokurdach |
| Ora solare Magadan | (UTC + 11.00) Magadan |
| Ora solare Norfolk | (UTC + 11.00) Isola di Norfolk |
| Ora solare Sakhalin | (UTC + 11.00) Sakhalin |
| Ora solare Pacifico centrale | (UTC + 11.00) Solomon è., Nuova Caledonia |
| Fuso orario Russia 11 | (UTC + 12.00 H) Anadyr, Petropavlovsk-Kamchatsky |
| Ora solare Nuova Zelanda | (UTC + 12.00 H) Auckland, Wellington |
| UTC + 12 | (UTC + 12.00 H) Coordinated Universal Time + 12 |
| Ora solare Figi | (UTC + 12.00 H) Fiji |
| Ora solare Kamcatka | (UTC + 12.00 H) Petropavlovsk-Kamchatsky-vecchio |
| Ora solare Isole Chatham | (UTC + 12:45) Isole Chatham |
| UTC + 13 | (UTC + 13.00) Ora UTC (Coordinated Universal Time) + 13 |
| Ora solare Tonga | (UTC + 13.00) Nuku ' alofa |
| Ora solare Samoa | (UTC + 13.00) Samoa |
| Ora solare isole line | (UTC + 14:00) Isola di Kiritimati |

## <a name="see-also"></a>Vedere anche 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys. time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
