---
title: Gruppi di computer nelle query di log in Monitoraggio di Azure | Microsoft Docs
description: I gruppi di computer in Monitoraggio di Azure consentono di limitare l'ambito delle query di log a uno specifico set di computer.  Questo articolo descrive i diversi metodi disponibili per creare gruppi di computer e come usarli in una query di log.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: c2babb5a86d69881b6a76c6dceae80a24a891f6c
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549304"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Gruppi di computer nelle query di log di monitoraggio di Azure
I gruppi di computer in Monitoraggio di Azure consentono di limitare l'ambito delle [query di log](../log-query/log-query-overview.md) a uno specifico set di computer.  Ogni gruppo viene popolato con i computer usando una query definita dall'utente oppure importando gruppi da diverse origini.  Quando il gruppo viene incluso in una query di log, i risultati sono limitati ai record corrispondenti ai computer del gruppo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Creazione di un gruppo di computer
È possibile creare un gruppo di computer in Monitoraggio di Azure usando uno dei metodi riportati nella tabella seguente.  Informazioni dettagliate su ogni metodo sono disponibili nelle sezioni successive. 

| Metodo | DESCRIZIONE |
|:--- |:--- |
| Query di log |Creare una query di log che restituisca un elenco di computer. |
| API di ricerca nei log |Usare l'API di ricerca log per creare un gruppo di computer a livello di codice in base ai risultati di una query di log. |
| Active Directory |Analizzare automaticamente l'appartenenza a gruppi di tutti i computer degli agenti che sono membri di un dominio di Active Directory e creare un gruppo in Monitoraggio di Azure per ogni gruppo di sicurezza. (solo computer Windows)|
| Gestione configurazione | Importare raccolte da System Center Configuration Manager e creare in Monitoraggio di Azure un gruppo per ognuna. |
| Windows Server Update Services |Analizzare automaticamente i server o i client WSUS per rilevare i gruppi di destinazione e creare in Monitoraggio di Azure un gruppo per ognuno. |

### <a name="log-query"></a>Query di log
I gruppi di computer creati da una query di log contengono tutti i computer restituiti da una query definita dall'utente.  Questa query viene eseguita ogni volta che viene usato il gruppo di computer, in modo da riflettere le modifiche successive alla creazione del gruppo.  

Per un gruppo di computer è possibile usare qualsiasi query, ma questa deve restituire un set distinto di computer tramite `distinct Computer`.  Ecco un esempio tipico di query che è possibile usare per un gruppo di computer.

    Heartbeat | where Computer contains "srv" | distinct Computer

Eseguire questa procedura per creare un gruppo di computer da una ricerca log nel portale di Azure.

1. Fare clic su **Log** nel menu **Monitoraggio di Azure** nel portale di Azure.
1. Creare ed eseguire una query che restituisca i computer desiderati nel gruppo.
1. Fare clic su **Salva** nella parte superiore della schermata.
1. Cambiare **Salva con nome** in **Funzione** e selezionare **Salva questa query come gruppo di computer**.
1. Specificare i valori di ogni proprietà per il gruppo di computer descritto nella tabella e fare clic su **Salva**.

La tabella seguente descrive le proprietà che definiscono un gruppo di computer.

| Proprietà | DESCRIZIONE |
|:---|:---|
| NOME   | Nome della query da visualizzare nel portale. |
| Alias di funzione | Alias univoco usato per identificare il gruppo di computer in una query. |
| Categoria       | Categoria per l'organizzazione delle query nel portale. |


### <a name="active-directory"></a>Active Directory
Quando si configura Monitoraggio di Azure per importare le appartenenze ai gruppi di Active Directory, viene analizzata l'appartenenza ai gruppi di tutti i computer aggiunti a un dominio Windows con l'agente Log Analytics.  Viene creato un gruppo di computer in Monitoraggio di Azure per ogni gruppo di sicurezza in Active Directory e ogni computer Windows viene aggiunto ai gruppi di computer corrispondenti ai gruppi di sicurezza di cui è membro.  L'appartenenza viene aggiornata continuamente ogni 4 ore.  

> [!NOTE]
> I gruppi di Active Directory importati contengono solo computer Windows.

È possibile configurare Monitoraggio di Azure per l'importazione dei gruppi di sicurezza di Active Directory in **Impostazioni avanzate** nell'area di lavoro Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer**, **Active Directory** e quindi **Importa le appartenenze a gruppi di Active Directory dai computer**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da Active Directory](media/computer-groups/configure-activedirectory.png)

Al termine dell'importazione dei gruppi, nel menu vengono elencati il numero dei computer di cui sono state rilevate le appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Quando si configura Monitoraggio di Azure per importare le appartenenze a gruppi WSUS, viene analizzata l'appartenenza a gruppi di destinazione di tutti i computer con l'agente Log Analytics.  Se si usa la destinazione lato client, vengono importate in Monitoraggio di Azure le appartenenze a gruppi di tutti i computer connessi a Monitoraggio di Azure che fanno parte di qualsiasi gruppo di destinazione di WSUS. Se si usa la destinazione lato server, per poter importare le informazioni relative all'appartenenza a gruppi in Monitoraggio di Azure è necessario che l'agente Log Analytics sia installato nel server WSUS.  L'appartenenza viene aggiornata continuamente ogni 4 ore. 

È possibile configurare Monitoraggio di Azure per l'importazione di gruppi WSUS in **Impostazioni avanzate** nell'area di lavoro Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer**, **WSUS** e quindi **Importa appartenenze a gruppi WSUS**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da WSUS](media/computer-groups/configure-wsus.png)

Al termine dell'importazione dei gruppi, nel menu vengono elencati il numero dei computer di cui sono state rilevate le appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Quando si configura Monitoraggio di Azure per l'importazione delle appartenenze a raccolte di Configuration Manager, viene creato un gruppo di computer per ogni raccolta.  Le informazioni di appartenenza delle raccolte vengono recuperate ogni 3 ore per mantenere aggiornati i gruppi di computer. 

Prima di importare raccolte di Configuration Manager è necessario [connettere Configuration Manager a Monitoraggio di Azure](collect-sccm.md).  È quindi possibile configurare l'importazione da **Impostazioni avanzate** nell'area di lavoro Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer**, **SCCM** e quindi **Importa appartenenze alla raccolta di Configuration Manager**.  Non è richiesta alcuna ulteriore configurazione.

![Gruppi di computer da SCCM](media/computer-groups/configure-sccm.png)

Dopo l'importazione delle raccolte, nel menu sono elencati il numero dei computer per i quali sono state rilevate appartenenze a gruppi e il numero dei gruppi importati.  È possibile fare clic su uno di questi collegamenti per ottenere i record **ComputerGroup** con tali informazioni.

## <a name="managing-computer-groups"></a>Gestione dei gruppi di computer
È possibile visualizzare i gruppi di computer creati tramite una query di log o tramite l'API di ricerca log dalle **Impostazioni avanzate** nell'area di lavoro Log Analytics nel portale di Azure.  Selezionare **Gruppi di computer** e quindi **Gruppi salvati**.  

Fare clic sulla **x** nella colonna **Rimuovi** per eliminare il gruppo di computer.  Fare clic sull'icona **Visualizza membri** in corrispondenza di un gruppo per eseguire la ricerca nei log del gruppo che ne restituisce i membri.  Non è possibile modificare un gruppo di computer. È necessario eliminarlo e quindi ricrearlo con le impostazioni modificate.

![Gruppi di computer salvati](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Uso di un gruppo di computer in una query di log
È possibile usare gruppo di computer creato da una query di log in una query trattando il relativo alias come una funzione, in genere con la sintassi seguente:

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




## <a name="computer-group-records"></a>Record dei gruppi di computer
Per ogni appartenenza a gruppi di computer creata da Active Directory o WSUS viene creato un record nell'area di lavoro Log Analytics.  Il tipo di questi record è **ComputerGroup** e le proprietà sono elencate nella tabella seguente.  Per i gruppi di computer basati su query di log non vengono creati record.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nome del computer membro. |
| `Group` |Nome del gruppo. |
| `GroupFullName` |Percorso completo del gruppo, con origine e nome dell'origine. |
| `GroupSource` |Origine da cui il gruppo è stato raccolto. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nome dell'origine da cui il gruppo è stato raccolto.  Per Active Directory, corrisponde al nome del dominio. |
| `ManagementGroupName` |Nome del gruppo di gestione per gli agenti SCOM.  Per gli altri agenti, corrisponde ad AOI-\<ID area di lavoro\> |
| `TimeGenerated` |Data e ora in cui il gruppo di computer è stato creato o aggiornato. |

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  

