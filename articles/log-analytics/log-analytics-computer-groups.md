---
title: Gruppi di computer nelle ricerche nei log in Log Analytics | Microsoft Docs
description: I gruppi di computer in Log Analytics consentono di limitare l'ambito delle ricerche nei log a uno specifico set di computer.  Questo articolo descrive i diversi metodi disponibili per creare gruppi di computer e come usare tali gruppi in una ricerca nei log.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: bwren
ms.openlocfilehash: 17a59a38b6a445a7f42df171a711669f95fc84c2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Gruppi di computer nelle ricerche nei log in Log Analytics

I gruppi di computer in Log Analytics consentono di limitare l'ambito delle [ricerche nei log](log-analytics-log-search-new.md) a uno specifico set di computer.  Ogni gruppo viene popolato con i computer usando una query definita dall'utente oppure importando gruppi da diverse origini.  Quando il gruppo viene incluso in una ricerca nei log, i risultati sono limitati ai record corrispondenti ai computer del gruppo.

## <a name="creating-a-computer-group"></a>Creazione di un gruppo di computer
È possibile creare un gruppo di computer in Log Analytics usando uno dei metodi riportati nella tabella seguente.  Informazioni dettagliate su ogni metodo sono disponibili nelle sezioni successive. 

| Metodo | Descrizione |
|:--- |:--- |
| Ricerca log |Creare una ricerca log che restituisca un elenco di computer. |
| API di ricerca nei log |Usare l'API di ricerca nei log per creare un gruppo di computer a livello di codice in base ai risultati di una ricerca nei log. |
| Active Directory |Analizzare automaticamente l'appartenenza a gruppi di tutti i computer degli agenti che sono membri di un dominio di Active Directory e creare un gruppo in Log Analytics per ogni gruppo di sicurezza. |
| Gestione configurazione | Importare raccolte da System Center Configuration Manager e creare in Log Analytics un gruppo per ognuna. |
| Windows Server Update Services |Analizzare automaticamente i server o i client WSUS per rilevare i gruppi di destinazione e creare in Log Analytics un gruppo per ognuno. |

### <a name="log-search"></a>Ricerca log
I gruppi di computer creati da una ricerca log contengono tutti i computer restituiti da una query definita dall'utente.  Questa query viene eseguita ogni volta che viene usato il gruppo di computer, in modo da riflettere le modifiche successive alla creazione del gruppo.  

Per un gruppo di computer è possibile usare qualsiasi query, ma questa deve restituire un set distinto di computer tramite `distinct Computer`.  Ecco un esempio tipico di ricerca che è possibile usare per un gruppo di computer.

    Heartbeat | where Computer contains "srv" | distinct Computer

La tabella seguente descrive le proprietà che definiscono un gruppo di computer.

| Proprietà | Descrizione |
|:---|:---|
| Nome visualizzato   | Nome della ricerca da visualizzare nel portale. |
| Categoria       | Categoria per l'organizzazione delle ricerche nel portale. |
| Query          | Query per il gruppo di computer. |
| Alias di funzione | Alias univoco usato per identificare il gruppo di computer in una query. |

Eseguire questa procedura per creare un gruppo di computer da una ricerca log nel portale di Azure.

2. Aprire **Ricerca log** e quindi fare clic su **Ricerche salvate** nella parte superiore della schermata.
3. Fare clic su **Aggiungi** e specificare i valori di ogni proprietà del gruppo di computer.
4. Selezionare **Salva questa query come gruppo di computer** e fare clic su **OK**.


Eseguire la procedura seguente per creare un gruppo di computer da una ricerca log nel portale di OMS.

1. Aprire **Ricerca log** e creare la ricerca log per il gruppo di computer.  
2. Fare clic sul pulsante **Salva** nella parte superiore della schermata.
3. Selezionare **Sì** per impostare l'opzione **Salva questa query come gruppo di computer**.
5. Specificare i valori di ogni proprietà del gruppo di computer. 


>[!NOTE]
> Se l'area di lavoro usa ancora il [linguaggio di query legacy di Log Analytics](log-analytics-log-search-upgrade.md), è possibile usare la stessa procedura per la creazione di un gruppo di computer. È tuttavia necessario usare la sintassi del linguaggio di query legacy.


### <a name="log-search-api"></a>API di ricerca nei log
I gruppi di computer creati con l'API di ricerca nei log sono uguali alle ricerche create con una ricerca nei log.  Per informazioni dettagliate sulla creazione di un gruppo di computer con l'API di ricerca nei log, vedere la sezione [Gruppi di computer in API REST di Log Analytics per la ricerca nei log](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Quando si configura Log Analytics per importare le appartenenze ai gruppi di Active Directory, viene analizzata l'appartenenza ai gruppi di tutti i computer aggiunti a un dominio con l'agente OMS.  Viene creato un gruppo di computer in Log Analytics per ogni gruppo di sicurezza in Active Directory e ogni computer viene aggiunto ai gruppi di computer corrispondenti ai gruppi di sicurezza di cui è membro.  L'appartenenza viene aggiornata continuamente ogni 4 ore.  

È possibile configurare Log Analytics per l'importazione dei gruppi di sicurezza di Active Directory in **Impostazioni avanzate** di Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer**, **Active Directory** e quindi **Importa le appartenenze a gruppi di Active Directory dai computer**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Al termine dell'importazione dei gruppi, nel menu vengono elencati il numero dei computer di cui sono state rilevate le appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Quando si configura Log Analytics per importare le appartenenze a gruppi di WSUS, viene analizzata l'appartenenza a gruppi di destinazione di tutti i computer con l'agente OMS.  Se si usa la destinazione lato client, vengono importate in Log Analytics le appartenenze a gruppi di tutti i computer connessi a OMS che fanno parte di qualsiasi gruppo di destinazione di WSUS. Se si usa la destinazione lato server, per poter importare le informazioni relative all'appartenenza a gruppi in OMS è necessario che l'agente OMS sia installato nel server WSUS.  L'appartenenza viene aggiornata continuamente ogni 4 ore. 

È possibile configurare Log Analytics per l'importazione di gruppi WSUS in **Impostazioni avanzate** di Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer**, **WSUS** e quindi **Importa appartenenze a gruppi WSUS**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Al termine dell'importazione dei gruppi, nel menu vengono elencati il numero dei computer di cui sono state rilevate le appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Quando si configura Log Analytics per l'importazione delle appartenenze a raccolte di Configuration Manager, Log Analytics crea un gruppo di computer per ogni raccolta.  Le informazioni di appartenenza delle raccolte vengono recuperate ogni 3 ore per mantenere aggiornati i gruppi di computer. 

Prima di importare raccolte di Configuration Manager è necessario [connettere Configuration Manager a Log Analytics](log-analytics-sccm.md).  È quindi possibile configurare l'importazione da **Impostazioni avanzate** di Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer**, **SCCM** e quindi **Importa appartenenze alla raccolta di Configuration Manager**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Dopo l'importazione delle raccolte, nel menu sono elencati il numero dei computer per i quali sono state rilevate appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

## <a name="managing-computer-groups"></a>Gestione dei gruppi di computer
È possibile visualizzare i gruppi di computer creati tramite una ricerca log o tramite l'API di ricerca log dalle **Impostazioni avanzate** di Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer** e quindi **Gruppi salvati**.  

Fare clic sulla **x** nella colonna **Rimuovi** per eliminare il gruppo di computer.  Fare clic sull'icona **Visualizza membri** in corrispondenza di un gruppo per eseguire la ricerca nei log del gruppo che ne restituisce i membri.  Non è possibile modificare un gruppo di computer. È necessario eliminarlo e quindi ricrearlo con le impostazioni modificate.

![Gruppi di computer salvati](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Uso di un gruppo di computer in una ricerca nei log
È possibile usare gruppo di computer creato da una ricerca log in una query trattando il relativo alias come una funzione, in genere con la sintassi seguente:

  `Table | where Computer in (ComputerGroup)`

È ad esempio possibile usare quanto segue per restituire record UpdateSummary solo dei computer in un gruppo di computer denominato mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


I gruppi di computer importati e i computer in essi inclusi vengono archiviati nella tabella **ComputerGroup**.  Ad esempio, la query seguente restituirà un elenco di computer nel gruppo Computer del dominio da Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

La query seguente restituisce i record UpdateSummary solo per i computer in Computer del dominio.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```



  

>[!NOTE]
> Se l'area di lavoro usa ancora il [linguaggio di query legacy di Log Analytics](log-analytics-log-search-upgrade.md)>, per fare riferimento a un gruppo di computer in una ricerca log usare la sintassi seguente.  Specificare **Category**> è facoltativo ed è necessario solo se sono presenti gruppi di computer con lo stesso nome in categorie diverse. 
>
>    `$ComputerGroups[Category: Name]`
>
>Nella ricerca log, i gruppi di computer vengono in genere usati con la clausola **IN**, come nell'esempio seguente:
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



## <a name="computer-group-records"></a>Record dei gruppi di computer
Per ogni appartenenza a gruppi di computer creata da Active Directory o WSUS viene creato un record nel repository OMS.  Il tipo di questi record è **ComputerGroup** e le proprietà sono elencate nella tabella seguente.  Per i gruppi di computer basati su ricerche nei log non vengono creati record.

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computer |Nome del computer membro. |
| Group |Nome del gruppo. |
| GroupFullName |Percorso completo del gruppo, con origine e nome dell'origine. |
| GroupSource |Origine da cui il gruppo è stato raccolto. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nome dell'origine da cui il gruppo è stato raccolto.  Per Active Directory, corrisponde al nome del dominio. |
| ManagementGroupName |Nome del gruppo di gestione per gli agenti SCOM.  Per gli altri agenti, corrisponde ad AOI-\<ID area di lavoro\> |
| TimeGenerated |Data e ora in cui il gruppo di computer è stato creato o aggiornato. |

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  

