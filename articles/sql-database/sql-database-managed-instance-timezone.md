---
title: Fusi orari dell'istanza gestita
description: Informazioni sulle specifiche del fuso orario dell'istanza gestita del database SQL di AzureLearn about the time zone specifics of Azure SQL Database Managed Instance
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256094"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusi orari nell'istanza gestita del database SQL di AzureTime zones in Azure SQL Database Managed Instance

L'ora UTC (Coordinated Universal Time) è il fuso orario consigliato per il livello dati delle soluzioni cloud. Istanza gestita database SQL di Azure offre anche una scelta di fusi orari per soddisfare le esigenze delle applicazioni esistenti che archiviano i valori di data e ora e le funzioni di data e ora di chiamata con un contesto implicito di un fuso orario specifico.

Le funzioni T-SQL come [getDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) o il codice CLR osservano il fuso orario impostato a livello di istanza. I processi di SQL Server Sql ServerSQL Server Agent seguono anche le pianificazioni in base al fuso orario dell'istanza.

  >[!NOTE]
  > Istanza gestita è l'unica opzione di distribuzione del database SQL di Azure che supporta l'impostazione del fuso orario. Altre opzioni di distribuzione seguono sempre l'ora UTC.
Se è necessario interpretare le informazioni di data e ora in un fuso orario non UTC, utilizzare il fuso orario [DI AT](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) .

## <a name="supported-time-zones"></a>Fusi orari supportati

Un set di fusi orari supportati viene ereditato dal sistema operativo sottostante dell'istanza gestita. Viene aggiornato regolarmente per ottenere nuove definizioni del fuso orario e riflettere le modifiche a quelle esistenti.

La politica di [modifica dell'ora legale/fuso orario](https://aka.ms/time) garantisce l'accuratezza storica a partire dal 2010.

Un elenco con i nomi dei fusi orari supportati viene esposto tramite la vista di sistema [sys.time_zone_info.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

## <a name="set-a-time-zone"></a>Impostare un fuso orario

È possibile impostare un fuso orario di un'istanza gestita solo durante la creazione dell'istanza. Il fuso orario predefinito è UTC.

  >[!NOTE]
  > Il fuso orario di un'istanza gestita esistente non può essere modificato.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Impostare il fuso orario tramite il portale di AzureSet the time zone through the Azure portal

Quando si immettono parametri per una nuova istanza, selezionare un fuso orario dall'elenco dei fusi orari supportati.
  
![Impostazione di un fuso orario durante la creazione dell'istanzaSetting a time zone during instance creation](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Specificare la proprietà timezoneId nel [modello di Resource Manager](https://aka.ms/sql-mi-create-arm-posh) per impostare il fuso orario durante la creazione dell'istanza.

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

Un elenco di valori supportati per la proprietà timezoneId si trova alla fine di questo articolo.

Se non specificato, il fuso orario è impostato su UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Controllare il fuso orario di un'istanza

La funzione [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) restituisce un nome visualizzato del fuso orario dell'istanza.

## <a name="cross-feature-considerations"></a>Considerazioni sulle funzionalità trasversali

### <a name="restore-and-import"></a>Ripristino e importazione

È possibile ripristinare un file di backup o importare dati in un'istanza gestita da un'istanza o da un server con impostazioni di fuso orario diverse. Assicurarsi di farlo con cautela. Analizzare il comportamento dell'applicazione e i risultati delle query e dei report, proprio come quando si trasferiscono dati tra due istanze di SQL ServerSQL Server con impostazioni di fuso orario diverse.

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Quando si esegue un ripristino temporizzato, il tempo in cui eseguire il ripristino viene interpretato come ora UTC. In questo modo vengono evitate tutte le ambiguità dovute all'ora legale e alle sue potenziali modifiche.

### <a name="auto-failover-groups"></a>Gruppi di failover automatico

L'uso dello stesso fuso orario in un'istanza primaria e secondaria in un gruppo di failover non viene applicato, ma è consigliabile.

  >[!WARNING]
  > È consigliabile usare lo stesso fuso orario per l'istanza primaria e secondaria in un gruppo di failover. A causa di alcuni casi di utilizzo rari che mantengono lo stesso fuso orario tra le istanze primaria e secondaria non viene applicato. È importante comprendere che nel caso del failover manuale o automatico, l'istanza secondaria manterrà il fuso orario originale.

## <a name="limitations"></a>Limitazioni

- Il fuso orario dell'istanza gestita esistente non può essere modificato.
- I processi esterni avviati dai processi di SQL Server Agent non osservano il fuso orario dell'istanza.

## <a name="list-of-supported-time-zones"></a>Elenco dei fusi orari supportati

| **ID fuso orario** | **Nome visualizzato fuso orario** |
| --- | --- |
| Datain Ora solare | (UTC-12:00) Linea Data Internazionale Ovest |
| UTC-11 | (UTC-11:00) Tempo universale coordinato-11 |
| Ora solare Aleeutiana | (UTC-10:00) Isole Aleuene |
| Ora solare hawaiano | (UTC-10:00) Hawaii |
| Ora solare Marchese | (UTC-09:30) Isole Marchesi |
| Ora solare Alaska | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Tempo universale coordinato-09 |
| Ora solare Pacifico (Messico) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Tempo universale coordinato-08 |
| Ora solare Pacifico | (UTC-08:00) Ora del Pacifico (US & Canada) |
| Ora solare US Mountain | (UTC-07:00) Arizona |
| Ora solare montagna (Messico) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Ora solare montagna | (UTC-07:00) Tempo di montagna (Stati Uniti & Canada) |
| Ora solare America centrale | (UTC-06:00) America centrale |
| Ora solare fuso centrale | (UTC-06:00) Ora centrale (Stati Uniti & Canada) |
| Ora solare Isola di Pasqua | (UTC-06:00) Isola di Pasqua |
| Ora solare fuso centrale (Messico) | (UTC-06:00) Guadalajara, Città del Messico, Monterrey |
| Ora solare Canada | (UTC-06:00) Saskatchewan |
| Ora solare Pacifico SA | (UTC-05:00) Bogotà, Lima, Quito, Rio Branco |
| Ora solare fuso orientale (Messico) | (UTC-05:00) Chetumal |
| Ora solare fuso orientale | (UTC-05:00) Eastern Time (Stati Uniti & Canada) |
| Ora solare Haiti | (UTC-05:00) Haiti |
| Ora solare Cuba | (UTC-05:00) L'Avana |
| Ora solare Stati Uniti orientali | (UTC-05:00) Indiana (Est) |
| Turks e Caicos Ora solare | (UTC-05:00) Turchi e Caicos |
| Ora solare Paraguay | (UTC-04:00) Asuncion |
| Ora solare Atlantico | (UTC-04:00) Ora atlantica (Canada) |
| Ora solare Venezuela | (UTC-04:00) Caracas |
| Ora solare brasiliana centrale | (UTC-04:00) Cuiaba |
| Ora solare SA Occidentale | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Ora solare Pacifico SA | (UTC-04:00) Santiago |
| Ora solare Terranova | (UTC-03:30) Terranova |
| Ora solare Tocantins | (UTC-03:00) Araguaina |
| E. Ora solare Sud America | (UTC-03:00) Brasilia |
| Ora solare SA orientale | (UTC-03:00) Cayenne |
| Ora solare Argentina | (UTC-03:00) Città di Buenos Aires |
| Ora solare Groenlandia | (UTC-03:00) Groenlandia |
| Ora solare Montevideo | (UTC-03:00) Montevideo |
| Ora solare Magallanes | (UTC-03:00) Arene di Punta |
| Ora solare Saint Pierre | (UTC-03:00) Saint Pierre e Miquelon |
| Ora solare Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Tempo universale coordinato-02 |
| Ora solare Medio Atlantico | (UTC-02:00) Medio Atlantico - Vecchio |
| Ora solare azzorre | (UTC-01:00) Azzorre |
| Ora solare Capo Verde | (UTC-01:00) Cabo Verde è. |
| UTC | (UTC) Coordinated Universal Time |
| Ora solare GMT | (UTC: 00:00) Dublino, Edimburgo, Lisbona, Londra |
| Ora solare di Greenwich | (UTC: 00:00) Monrovia |
| W. Ora solare Europa | (UTC: 01:00) Amsterdam, Berlino, Berna, Roma, Stoccolma, Vienna |
| Ora solare Europa centrale | (UTC: 01:00) Belgrado, Bratislava, Budapest, Lubiana, Praga |
| Ora solare Romance | (UTC: 01:00) Bruxelles, Copenaghen, Madrid, Parigi |
| Ora solare Marocco | (UTC: 01:00) Casablanca |
| Ora solare Sao Tome | (UTC: 01:00) Sao Tomo |
| Ora solare Europa centrale | (UTC: 01:00) Sarajevo, Skopje, Varsavia |
| W. Ora solare Africa centrale | (UTC: 01:00) Africa centrale occidentale |
| Ora solare Giordania | (UTC: 02:00) Amman |
| Ora solare GTB | (UTC: 02:00) Atene |
| Ora solare Medio Oriente | (UTC: 02:00) Beirut |
| Ora solare Egitto | (UTC: 02:00) Cairo |
| E. Ora solare Europa | (UTC: 02:00) Chisinau |
| Ora solare Siria | (UTC: 02:00) Damasco |
| Ora solare Cisgiifazione | (UTC: 02:00) Gaza |
| Ora solare Sud Africa | (UTC: 02:00) Harare |
| Ora solare FLE | (UTC: 02:00) Helsinki, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Ora solare Israele | (UTC: 02:00) Gerusalemme |
| Ora solare Kaliningrad | (UTC: 02:00) Kaliningrad |
| Ora solare Sudan | (UTC: 02:00) Khartoum |
| Ora solare Libia | (UTC: 02:00) Tripoli |
| Ora solare Namibia | (UTC: 02:00) Windhoek |
| Ora solare arabo | (UTC: 03:00) Baghdad |
| Ora solare Turchia | (UTC: 03:00) Istanbul |
| Ora solare Araba | (UTC: 03:00) Kuwait, Riyadh |
| Ora solare Bielorussia | (UTC: 03:00) Minsk |
| Ora solare russa | (UTC: 03:00) Mosca, San Pietroburgo |
| E. Ora solare Africa | (UTC: 03:00) Nairobi |
| Ora solare Iran | (UTC: 03:30) Teheran |
| Ora solare Araba | (UTC: 04:00) Abu Dhabi |
| Ora solare Astrakhan | (UTC: 04:00) Astrakhan |
| Ora solare Azerbaigian | (UTC: 04:00) Baku |
| Fuso orario Russia 3 | (UTC: 04:00) Izhevsk |
| Ora solare Mauritius | (UTC: 04:00) Port Louis |
| Ora solare Saratov | (UTC: 04:00) Saratov |
| Ora solare georgiano | (UTC: 04:00) Tbilisi |
| Ora solare Volgograd | (UTC: 04:00) Volgograd |
| Ora solare Caucaso | (UTC: 04:00) Yerevan |
| Ora solare Afghanistan | (UTC: 04:30) Kabul |
| Ora solare Asia occidentale | (UTC: 05:00) Ashgabat |
| Ora solare Ekaterinburg | (UTC: 05:00) Ekaterinburg |
| Ora solare Pakistan | (UTC: 05:00) Islamabad |
| Ora solare India | (UTC: 05:30) Chennai, Kolkata, Mumbai, Nuova Delhi |
| Ora solare Sri Lanka | (UTC: 05:30) Sri Jayawardenepura |
| Ora solare Nepal | (UTC: 05:45) Kathmandu |
| Ora solare Asia centrale | (UTC- 06:00) Astana |
| Ora solare Bangladesh | (UTC- 06:00) Dhaka |
| Ora solare Omsk | (UTC- 06:00) Omsk |
| Ora solare Myanmar | (UTC: 06:30) Yangon |
| Ora solare SE Asia | (UTC: 07:00) Bangkok, Hanoi, Giacarta |
| Ora solare Altai | (UTC: 07:00) Barnaul |
| W. Ora solare Mongolia | (UTC: 07:00) Hovd |
| Ora solare Asia settentrionale | (UTC: 07:00) Krasnoyarsk |
| N. Ora solare Asia centrale | (UTC: 07:00) Novosibirsk |
| Ora solare Tomsk | (UTC: 07:00) Tomsk |
| Ora solare Cina | (UTC: 08:00) Pechino, Chongqing, Hong Kong, Urumqi |
| Ora solare Asia settentrionale orientale | (UTC: 08:00) Irkutsk |
| Ora solare di Singapore | (UTC: 08:00) Kuala Lumpur, Singapore |
| W. Ora solare Australia | (UTC: 08:00) Perth |
| Ora solare Taipei | (UTC: 08:00) Taipei |
| Ora solare Ulaanbaatar | (UTC: 08:00) Ulaanbaatar |
| Ora solare aus Central W. | (UTC: 08:45) Eucla |
| Ora solare transbaikal | (UTC: 09:00) Chita |
| Ora solare Tokyo | (UTC: 09:00) Osaka, Sapporo, Tokyo |
| Ora solare Corea del Nord | (UTC: 09:00) Pyongyang |
| Ora solare Corea | (UTC: 09:00) Seoul |
| Ora solare Yakutsk | (UTC: 09:00) Yakutsk |
| Cen. Ora solare Australia | (UTC: 09:30) Adelaide |
| Ora solare aUS centrale | (UTC: 09:30) Darwin |
| E. Ora solare Australia | (UTC - 10:00) Brisbane |
| Ora solare AUS orientale | (UTC - 10:00) Canberra, Melbourne, Sydney |
| Ora solare Pacifico occidentale | (UTC - 10:00) Guam |
| Ora solare Tasmania | (UTC - 10:00) Hobart |
| Ora solare Vladivostok | (UTC - 10:00) Vladivostok |
| Ora solare Lord Howe | (UTC - 10:30) Isola Lord Howe |
| Ora solare Bougainville | (UTC- 11:00) Isola Bougainville |
| Fuso orario Russia 10 | (UTC- 11:00) Chokurdakh |
| Ora solare Magadan | (UTC- 11:00) Magadan |
| Ora solare Norfolk | (UTC- 11:00) Isola Norfolk |
| Ora solare Sakhalin | (UTC- 11:00) Sakhalin |
| Ora solare Pacifico centrale | (UTC- 11:00) Salomone Is., Nuova Caledonia |
| Fuso orario Russia 11 | (UTC - 12:00) Anadyr |
| Ora solare Italia | (UTC - 12:00) Auckland |
| UTC 12 | (UTC - 12:00) Tempo Universale Coordinato 12 |
| Ora solare Figi | (UTC - 12:00) Fiji |
| Ora solare Kamchatka | (UTC - 12:00) Petropavlovsk-Kamchatsky - Vecchio |
| Ora solare Isole Chatham | (UTC- 12:45) Isole Chatham |
| UTC 13 | (UTC- 13:00) Tempo Universale Coordinato 13 |
| Ora solare Tonga | (UTC- 13:00) Nuku'alofa |
| Ora solare Samoa | (UTC- 13:00) Samoa |
| Ora solare Isole Line | (UTC- 14:00) Isola Kiritimati |

## <a name="see-also"></a>Vedere anche 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
