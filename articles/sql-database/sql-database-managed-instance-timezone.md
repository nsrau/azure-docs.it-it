---
title: Istanza gestita di Database SQL Azure fusi orari | Microsoft Docs"
description: Informazioni sulle specifiche fuso orario dell'istanza gestita di Azure SQL Database
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/25/2019
ms.openlocfilehash: 6d7d065f45bca38cedd2c276bdd9b98dfd9675df
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866940"
---
# <a name="time-zones-in-azure-sql-database-managed-instance-preview"></a>Fusi orari in SQL Database istanza gestita di Azure (anteprima)

Coordinated Universal Time (UTC) è il fuso orario consigliato per il livello di dati di soluzioni cloud. Istanza gestita del Database SQL Azure offre anche una scelta dei fusi orari per soddisfare le esigenze delle applicazioni esistenti che archiviano i valori di data e ora e chiamano le funzioni di data e ora con un contesto implicita di un fuso orario specifico.

T-SQL funziona in modo analogo [GETDATE ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) o codice CLR osservare il fuso orario impostato sull'istanza. Processi di SQL Server Agent seguono anche le pianificazioni in base al fuso orario dell'istanza.

  >[!NOTE]
  > Istanza gestita è l'opzione di distribuzione solo del Database SQL di Azure che supporta l'impostazione del fuso orario. Altre opzioni di distribuzione seguono sempre UTC.
Uso [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) nel database SQL singoli e in pool se è necessario interpretare le informazioni di data e ora in un fuso orario non-UTC.

## <a name="supported-time-zones"></a>Fusi orari supportati

Un set di zone ora supportate verrà ereditato dal sistema operativo sottostante dell'istanza gestita. Venga aggiornata regolarmente per ottenere le nuove definizioni di fuso orario e riflettere le modifiche a quelli esistenti. 

Un elenco con i nomi dei fusi orari supportati viene esposta tramite il [Sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) vista di sistema.

## <a name="set-a-time-zone"></a>Impostare un fuso orario

Un fuso orario di un'istanza gestita può essere impostato durante la creazione di istanze soltanto. Il fuso orario predefinito è UTC.

  >[!NOTE]
  > Impossibile modificare il fuso orario di un'istanza gestita esistente.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Impostare il fuso orario nel portale di Azure

Quando si immettono i parametri per una nuova istanza, selezionare un fuso orario dall'elenco dei fusi orari supportati. 
  
![Impostazione di un fuso orario durante la creazione di istanze](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

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

Un elenco dei valori supportati per la proprietà timezoneId è alla fine di questo articolo.

Se non specificato, il fuso orario è impostato su UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Controllare il fuso orario di un'istanza

Il [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) funzione restituisce un nome visualizzato del fuso orario dell'istanza.

## <a name="cross-feature-considerations"></a>Considerazioni sulla tra funzionalità

### <a name="restore-and-import"></a>Ripristino e l'importazione

È possibile ripristinare un file di backup o importare dati in un'istanza gestita da un'istanza o un server con le impostazioni di fuso orario diverso. Assicurarsi di eseguire questa operazione con cautela. Analizzare il comportamento dell'applicazione e i risultati delle query e report, proprio come quando si trasferiscono dati tra due istanze di SQL Server con le impostazioni di fuso orario diverso.

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Quando si esegue un ripristino temporizzato in, il tempo per il ripristino viene interpretato come ora UTC. Questa impostazione consente di evitare qualsiasi ambiguità a causa dell'ora legale e le relative modifiche potenziali.

### <a name="auto-failover-groups"></a>Gruppi di failover automatico

Usando lo stesso fuso orario su un'istanza primaria e secondaria in un gruppo di failover non è applicata, ma è consigliabile utilizzarla.

  >[!WARNING]
  > È consigliabile usare lo stesso fuso orario per l'istanza primaria e secondaria in un gruppo di failover. A causa di alcuni scenari rari, mantenendo lo stesso fuso orario per le istanze principale e secondarie non è applicata. È importante comprendere che in caso di failover manuale o automatico, nell'istanza secondaria verrà mantenuto il fuso orario originale.

## <a name="limitations"></a>Limitazioni

- Impossibile modificare il fuso orario dell'istanza gestita esistente.
- I processi esterni avviati da processi di SQL Server Agent non osservare il fuso orario dell'istanza.

## <a name="list-of-supported-time-zones"></a>Elenco dei fusi orari supportati

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
| Isole Turks e Caicos solare | (UTC - 5.00 h) Turks e Caicos |
| Ora solare Paraguay | (UTC-04.00) Asunción |
| Ora solare costa atlantica | (UTC-04.00) Ora costa atlantica (Canada) |
| Ora solare Venezuela | (UTC-04.00) Caracas |
| Ora solare Brasile centrale | (UTC-04.00) Cuiaba |
| Ora solare America del Sud occidentale | (UTC-04.00) Georgetown, La Paz, Manaus, San Juan |
| Ora solare America del Sud Pacifico | (UTC-04.00) Santiago |
| Ora solare Terranova | (UTC-03.30) Terranova |
| Ora solare Tocantins | (UTC-03.00) Araguaina |
| E. Ora solare America del Sud | (UTC-03.00) Brasilia |
| Ora solare America del Sud orientale | (UTC-03.00) Caienna, Fortaleza |
| Ora solare Argentina | (UTC-03.00) Buenos Aires |
| Ora solare Groenlandia | (UTC-03.00) Groenlandia |
| Ora solare Montevideo | (UTC-03.00) Montevideo |
| Ora solare Magallanes | (UTC - 03.00 h) Punta Arenas |
| Ora solare Saint-Pierre | (UTC-03.00) Saint Pierre e Miquelon |
| Ora solare Bahia | (UTC-03.00) El Salvador |
| UTC-02 | (UTC-02.00) Coordinated Universal Time-02 |
| Ora solare Medioatlantico | (UTC-02.00) Medioatlantico - Vecchio |
| Ora solare Azzorre | (UTC-01.00) Azzorre |
| Ora solare Cabo Verde | (UTC-01.00) Is. di Cabo Verde |
| UTC | (UTC) Coordinated Universal Time |
| Ora solare di Greenwich | (UTC+00.00) Dublino, Edimburgo, Lisbona, Londra |
| Ora solare di Greenwich | (UTC+00.00) Monrovia, Reykjavik |
| W. Ora solare Europa | (UTC+01.00) Amsterdam, Berlino, Berna, Roma, Stoccolma, Vienna |
| Ora solare Europa Centrale | (UTC+01.00) Belgrado, Bratislava, Budapest, Lubiana, Praga |
| Ora solare Bruxelles, Copenaghen, Madrid, Parigi | (UTC+01.00) Bruxelles, Copenaghen, Madrid, Parigi |
| Ora solare Marocco | (UTC+01:00) Casablanca |
| Ora solare São Tomé | (UTC + 01.00) São Tomé |
| Ora solare Europa Centrale | (UTC+01.00) Sarajevo, Skopje, Varsavia, Zagabria |
| W. Ora solare Africa Central | (UTC+01.00) Africa centro-occidentale |
| Ora solare Giordania | (UTC+02.00) Amman |
| Ora solare GTB | (UTC+02.00) Atene, Bucarest |
| Ora solare Medio Oriente | (UTC+02.00) Beirut |
| Ora solare Egitto | (UTC+02.00) Cairo |
| E. Ora solare Europa | (UTC+02.00) Chisinau |
| Ora solare Siria | (UTC+02.00) Damasco |
| Ora solare Cisgiordania | (UTC+02.00) Hebron |
| Ora solare Sudafrica | (UTC+02.00) Harare, Pretoria |
| Ora solare FLE | (UTC+02.00) Helsinki, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israel Standard Time | (UTC+02.00) Gerusalemme |
| Ora solare Kaliningrad | (UTC+02.00) Kaliningrad |
| Ora solare Sudan | (UTC + 2.00 h) Khartoum |
| Ora solare Libia | (UTC+02.00) Tripoli |
| Ora solare Namibia | (UTC + 2.00 h) Windhoek |
| Ora solare Arabia Saudita | (UTC+03.00) Baghdad |
| Ora solare Turchia | (UTC + 03.00 h) Istanbul |
| Ora solare Arabia | (UTC+03.00) Kuwait, Riyadh |
| Ora solare Bielorussia | (UTC+03.00) Minsk |
| Ora solare Russia | (UTC+03:00) Mosca, S. Pietroburgo |
| E. Africa Standard Time | (UTC+03.00) Nairobi |
| Ora solare Iran | (UTC+03.30) Teheran |
| Ora solare Emirati Arabi Uniti | (UTC+04.00) Abu Dhabi, Mascate |
| Ora solare Astrakhan | (UTC+04.00) Astrakhan, Ulyanovsk |
| Ora solare Azerbaigian | (UTC+04.00) Baku |
| Fuso orario Russia 3 | (UTC+04.00) Izhevsk, Samara |
| Ora solare Mauritius | (UTC+04.00) Port Louis |
| Ora solare Saratov | (UTC + 4.00 h) Saratov |
| Ora solare Georgia | (UTC+04.00) Tbilisi |
| Ora solare Volgograd | (UTC+04:00) Volgograd |
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
| Ora solare Omsk | (UTC + 06.00 h) Omsk |
| Ora solare Myanmar | (UTC+06.30) Yangon (Rangoon) |
| Ora solare Asia sud-orientale | (UTC+07.00) Bangkok, Hanoi, Giacarta |
| Ora solare Altai | (UTC+07.00) Barnaul, Gorno-Altaysk |
| W. Ora solare Mongolia | (UTC+07.00) Hovd |
| Ora solare Asia settentrionale | (UTC+07.00) Krasnoyarsk |
| N. Ora solare Asia centrale | (UTC + 07.00 h) Novosibirsk |
| Ora solare Tomsk | (UTC+07.00) Tomsk |
| Ora solare Cina | (UTC+08:00) Beijing (Pechino), Chongqing, Hong Kong - R.A.S., Urumqi |
| Ora solare Asia nordorientale | (UTC+08.00) Irkutsk |
| Singapore Standard Time | (UTC+08.00) Kuala Lumpur, Singapore |
| W. Ora solare Europa occidentale | (UTC+08.00) Perth |
| Ora solare Taipei | (UTC+08.00) Taipei |
| Ora solare Ulan-Bator | (UTC+08.00) Ulan-Bator |
| Ora solare Austr. centro-occ. | (UTC+08.45) Eucla |
| Ora solare Transbaikal | (UTC+09.00) Chita |
| Ora solare Tokyo | (UTC+09.00) Osaka, Sapporo, Tokyo |
| Ora solare Corea del Nord | (UTC + 09.00) Pyongyang |
| Ora solare Corea | (UTC+09.00) Seoul |
| Ora solare Yakutsk | (UTC+09.00) Yakutsk |
| Cen. Ora solare Europa occidentale | (UTC+09.30) Adelaide |
| Ora solare Australia centrale | (UTC+09.30) Darwin |
| E. Ora solare Europa occidentale | (UTC+10.00) Brisbane |
| Ora solare Australia orientale | (UTC+10.00) Canberra, Melbourne, Sydney |
| Ora solare Pacifico occidentale | (UTC+10.00) Guam, Port Moresby |
| Ora solare Tasmania | (UTC+10.00) Hobart |
| Vladivostok Standard Time | (UTC+10.00) Vladivostok |
| Ora solare Lord Howe | (UTC+10.30) Isola Lord Howe |
| Ora solare Bougainville | (UTC+11.00) Bougainville |
| Fuso orario Russia 10 | (UTC+11.00) Chokurdakh |
| Ora solare Magadan | (UTC+11.00) Magadan |
| Ora solare Norfolk | (UTC+11.00) Norfolk |
| Ora solare Sakhalin | (UTC+11.00) Sakhalin |
| Ora solare Pacifico centrale | (UTC+11.00) Is. Salomone, Nuova Caledonia |
| Fuso orario Russia 11 | (UTC+12.00) Anadyr, Petropavlovsk-Kamčatskij |
| Ora solare Nuova Zelanda | (UTC+12.00) Auckland, Wellington |
| UTC+12 | (UTC+12.00) Coordinated Universal Time+12 |
| Ora solare Figi | (UTC+12.00) Figi |
| Ora solare Kamchatka | (UTC+12.00) Petropavlovsk-Kamčatskij - Vecchio |
| Ora solare Isole Chatham | (UTC+12.45) Isole Chatham |
| UTC+13 | (UTC + 13.00 h) Coordinated Universal Time+13 |
| Ora solare Tonga | (UTC+13.00) Nuku'alofa |
| Ora solare Samoa | (UTC+13.00) Samoa |
| Ora solare Isole Line | (UTC+14.00) Isola di Kiritimati |

## <a name="see-also"></a>Vedere anche  

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
