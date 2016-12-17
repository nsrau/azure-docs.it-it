---
title: Gruppi di computer nelle ricerche nei log in Log Analytics | Documentazione Microsoft
description: I gruppi di computer in Log Analytics consentono di limitare l&quot;ambito delle ricerche nei log a uno specifico set di computer.  Questo articolo descrive i diversi metodi disponibili per creare gruppi di computer e come usare tali gruppi in una ricerca nei log.
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
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6c0affd0f5ea600f979cfcc87e2435658c8dab14


---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Gruppi di computer nelle ricerche nei log in Log Analytics
I gruppi di computer in Log Analytics consentono di limitare l'ambito delle [ricerche nei log](log-analytics-log-searches.md) a uno specifico set di computer.  Ogni gruppo viene popolato con i computer usando una query definita dall'utente oppure importando gruppi da diverse origini.  Quando il gruppo viene incluso in una ricerca nei log, i risultati sono limitati ai record corrispondenti ai computer del gruppo.

## <a name="creating-a-computer-group"></a>Creazione di un gruppo di computer
È possibile creare un gruppo di computer in Log Analytics usando uno dei metodi riportati nella tabella seguente.  Informazioni dettagliate su ogni metodo sono disponibili nelle sezioni successive. 

| Metodo | Descrizione |
|:--- |:--- |
| Ricerca log |Creare una ricerca nei log che restituisca un elenco di computer e salvare i risultati come gruppo di computer. |
| API di ricerca nei log |Usare l'API di ricerca nei log per creare un gruppo di computer a livello di codice in base ai risultati di una ricerca nei log. |
| Active Directory |Analizzare automaticamente l'appartenenza a gruppi di tutti i computer degli agenti che sono membri di un dominio di Active Directory e creare un gruppo in Log Analytics per ogni gruppo di sicurezza. |
| WSUS |Analizzare automaticamente i server o i client WSUS per rilevare i gruppi di destinazione e creare in Log Analytics un gruppo per ognuno. |

### <a name="log-search"></a>Ricerca log
I gruppi di computer creati da una ricerca nei log conterranno tutti i computer restituiti da una query di ricerca definita dall'utente.  Questa query viene eseguita ogni volta che viene usato il gruppo di computer in modo da riflettere le modifiche successive alla creazione del gruppo.

Per creare un gruppo di computer da una ricerca nei log, seguire questa procedura.

1. [Creare una ricerca nei log](log-analytics-log-searches.md) che restituisca un elenco di computer.  La ricerca deve restituire un set distinto di computer usando ad esempio **distinct Computer** o **measure count() by Computer** nella query.  
2. Fare clic sul pulsante **Salva** nella parte superiore della schermata.
3. Selezionare **Sì** per impostare l'opzione **Salva questa query come gruppo di computer**.
4. Digitare **Nome** e **Categoria** del gruppo.  Se esiste già una ricerca con lo stesso nome e la stessa categoria, verrà richiesto se la si vuole sovrascrivere.  È possibile avere più ricerche con lo stesso nome in categorie diverse. 

Di seguito sono riportate ricerche di esempio che è possibile salvare come gruppo di computer.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>API di ricerca nei log
I gruppi di computer creati con l'API di ricerca nei log sono uguali alle ricerche create con una ricerca nei log.

Per informazioni dettagliate sulla creazione di un gruppo di computer con l'API di ricerca nei log, vedere la sezione [Gruppi di computer in API REST di Log Analytics per la ricerca nei log](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Quando si configura Log Analytics per importare le appartenenze a gruppi di Active Directory, viene analizzata l'appartenenza a gruppi di tutti i computer aggiunti a un dominio con l'agente OMS.  Viene creato un gruppo di computer in Log Analytics per ogni gruppo di sicurezza in Active Directory e ogni computer viene aggiunto ai gruppi di computer corrispondenti ai gruppi di sicurezza di cui è membro.  L'appartenenza viene aggiornata continuamente ogni 4 ore.  

È possibile configurare Log Analytics per l'importazione dei gruppi di sicurezza di Active Directory dal menu **Gruppi di computer** di **Impostazioni** in Log Analytics.  Selezionare **Automazione** e quindi **Importa le appartenenze a gruppi di Active Directory dai computer**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Al termine dell'importazione dei gruppi, nel menu saranno elencati il numero dei computer di cui sono state rilevate le appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Quando si configura Log Analytics per importare le appartenenze a gruppi di WSUS, viene analizzata l'appartenenza a gruppi di destinazione di tutti i computer con l'agente OMS.  Se si usa la destinazione lato client, verranno importate in Log Analytics le appartenenze a gruppi di tutti i computer connessi a OMS che fanno parte di qualsiasi gruppo di destinazione di WSUS. Se si usa la destinazione lato server, per poter importare le informazioni relative all'appartenenza a gruppi in OMS è necessario che l'agente OMS sia installato nel server WSUS.  L'appartenenza viene aggiornata continuamente ogni 4 ore. 

È possibile configurare Log Analytics per l'importazione dei gruppi di sicurezza di Active Directory dal menu **Gruppi di computer** di **Impostazioni** in Log Analytics.  Selezionare **Active Directory** e quindi **Importa le appartenenze a gruppi di Active Directory dai computer**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Al termine dell'importazione dei gruppi, nel menu saranno elencati il numero dei computer di cui sono state rilevate le appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

## <a name="managing-computer-groups"></a>Gestione dei gruppi di computer
È possibile visualizzare i gruppi di computer creati da una ricerca nei log o dall'API di ricerca nei log dal menu **Gruppi di computer** di **Impostazioni** in Log Analytics.  Fare clic sulla **x** nella colonna **Rimuovi** per eliminare il gruppo di computer.  Fare clic sull'icona **Visualizza membri** in corrispondenza di un gruppo per eseguire la ricerca nei log del gruppo che ne restituisce i membri. 

![Gruppi di computer salvati](media/log-analytics-computer-groups/configure-saved.png)

Per modificare il gruppo, creare un nuovo gruppo con la stessa **categoria** e lo stesso **nome** per sovrascrivere il gruppo originale.

## <a name="using-a-computer-group-in-a-log-search"></a>Uso di un gruppo di computer in una ricerca nei log
Per fare riferimento a un gruppo di computer in una ricerca nei log, usare la sintassi riportata di seguito.  Specificare **Category** è facoltativo ed è necessario solo se sono presenti gruppi di computer con lo stesso nome in categorie diverse. 

    $ComputerGroups[Category: Name]

Quando viene eseguita una ricerca, per prima cosa vengono risolti i membri di tutti i gruppi di computer inclusi nella ricerca.  Se il gruppo è basato su una ricerca nei log, viene eseguita tale ricerca per ottenere i membri del gruppo prima di eseguire la ricerca nei log di livello superiore.

Nella ricerca nei log, i gruppi di computer vengono in genere usati con la clausola **IN** come nell'esempio seguente.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

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
| GroupSourceName |Nome dell'origine da cui i gruppi sono stati raccolti.  Per Active Directory, corrisponde al nome del dominio. |
| ManagementGroupName |Nome del gruppo di gestione per gli agenti SCOM.  Per gli altri agenti, corrisponde ad AOI-\<ID area di lavoro\> |
| TimeGenerated |Data e ora in cui il gruppo di computer è stato creato o aggiornato. |

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  




<!--HONumber=Nov16_HO3-->


