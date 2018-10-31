---
title: Aggiornamento di Azure Stack 1809 | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 1809 per i sistemi integrati di Azure Stack, inclusi i problemi noti e su dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 38a8cd35d123f58d6379ade4d900e86b36b416ec
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240138"
---
# <a name="azure-stack-1809-update"></a>Aggiornamento di Azure Stack 1809

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo descrive il contenuto del pacchetto di aggiornamento 1809. Il pacchetto di aggiornamento include miglioramenti e correzioni di problemi noti per questa versione di Azure Stack. Questo articolo include anche un collegamento in modo che è possibile scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]  
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione

È il numero di build di aggiornamento di Azure Stack 1809 **1.1809.0.90**.  

### <a name="new-features"></a>Nuove funzionalità

Questo aggiornamento include i miglioramenti seguenti per Azure Stack:

- Con questa versione, Azure Stack integrati configurazioni supporta i sistemi di 4-16 nodi. È possibile usare la [Capacity Planner di Azure Stack](http://aka.ms/azstackcapacityplanner) per agevolare la pianificazione della capacità di Azure Stack e la configurazione.

- <!--  2712869   | IS  ASDK -->  **Syslog client di Azure Stack (disponibilità generale)** questa consente l'inoltro di controlli, avvisi e i log di sicurezza relativi all'infrastruttura di Azure Stack a un syslog server o della sicurezza le informazioni ed eventi (SIEM) software di gestione esterni ad Azure Stack. Il client di syslog supporta ora specificando la porta su cui è in ascolto il server syslog.

   Con questa versione, il client di syslog è disponibile a livello generale, e può essere utilizzato in ambienti di produzione.

   Per altre informazioni, vedere [l'inoltro di syslog di Azure Stack](azure-stack-integrate-security.md).

- Ora puoi [spostare la risorsa di registrazione](azure-stack-registration.md#move-a-registration-resource) in Azure tra gruppi di risorse senza dover registrare di nuovo. Cloud Solution Provider (CSP) possono anche spostare la risorsa di registrazione tra le sottoscrizioni, purché entrambe le sottoscrizioni di nuove e precedenti sono mappate allo stesso ID di partner CSP. Ciò non ha alcun impatto sui mapping dei tenant dei clienti esistenti. 

### <a name="fixed-issues"></a>Problemi risolti

<!-- TBD - IS ASDK -->
- Nel portale di reporting capacità usata/liberare il grafico di memoria è ora accurato. Quante VM si è in grado di creare a questo punto è possibile stimare in modo più affidabile.

<!-- TBD - IS ASDK --> 
- Risolto un problema in cui sono create macchine virtuali nel portale utenti Azure Stack e il portale è visualizzato un numero errato di dischi dati che è possibile collegare a una VM serie DS. Le VM serie DS può supportare tutti i dischi dati come la configurazione di Azure.

- I seguenti problemi di disco gestito sono stati risolti in 1809 e sono stati inoltre corretti nel 1808 [Azure Stack Hotfix 1.1808.5.110](https://support.microsoft.com/help/4468920/): 

   <!--  2966665 – IS, ASDK --> 
   - Risolto il problema in cui collegamento SSD i dischi dati premium di dimensioni delle macchine virtuali disco gestito (DS, DSv2, Fs, Fs_V2) non è riuscite con errore: *non è stato possibile aggiornare i dischi della macchina virtuale 'vmname' errore: non è possibile eseguire l'operazione perché richiesta tipo di account di archiviazione "Premium_LRS" non è supportata per dimensioni della macchina virtuale ' Standard_DS/Ds_V2/ADFS/Fs_v2)*. 
   
   - Creazione di un disco gestito della macchina virtuale tramite **createOption**: **Attach** avrà esito negativo con l'errore seguente: *operazione a esecuzione prolungata non è riuscita con stato 'Failed'. Informazioni aggiuntive: 'si è verificato un errore di esecuzione interno.'*
   ErrorCode: InternalExecutionError ErrorMessage: si è verificato un errore di esecuzione interno.
   
   Questo problema ora è stato risolto.

- <!-- 2702741 -  IS, ASDK --> Risolto un problema in cui gli indirizzi IP pubblici che sono stati distribuiti usando l'allocazione dinamica (metodo) non sono garantiti deve rimanere invariato dopo l'esecuzione di un arresto-deallocazione. Vengono ora mantenuti.

- <!-- 3078022 - IS, ASDK --> Se una macchina virtuale è stato di arresto-deallocazione prima 1808 causa potrebbe non essere riallocata dopo l'aggiornamento 1808.  Questo problema viene risolto in 1809. Le istanze che erano in questo stato e non è stato possibile avviare possono essere avviate in 1809 con questa correzione. La correzione impedisce inoltre il problema ricorrente.

### <a name="changes"></a>Modifiche

No.

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Questo aggiornamento installa gli aggiornamenti di sicurezza seguenti:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Per altre informazioni su questi problemi di protezione, fare clic su collegamenti precedenti o vedere gli articoli della Microsoft Knowledge Base [4457131](https://support.microsoft.com/help/4457131) e [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Prerequisiti

- Installare l'Hotfix di Stack più recente di Azure per 1808 prima di applicare 1809. Per altre informazioni, vedere [KB 4468920 – Azure Stack hotfix per Azure Stack Hotfix 1.1808.5.110](https://support.microsoft.com/en-us/help/4468920).

  > [!TIP]  
  > Sottoscrivere seguenti *RRS* oppure *Atom* i feed di stare al passo con gli aggiornamenti rapidi di Azure Stack:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con i parametri seguenti per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Quando si esegue [Test-AzureStack](azure-stack-diagnostic-test.md) dopo l'aggiornamento 1809, viene visualizzato un messaggio di avviso da Baseboard Management Controller (BMC). È possibile ignorare questo avviso.

- <!-- 2468613 - IS --> Durante l'installazione di questo aggiornamento, si potrebbero visualizzare avvisi con il titolo *Error: modello per FaultType UserAccounts.New è manca.*  È possibile ignorare questi avvisi. Questi avvisi chiuderà automaticamente al termine dell'installazione dell'aggiornamento.

- <!-- 2489559 - IS --> Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per altre informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Se è stato applicato un aggiornamento ad Azure Stack da OEM, il **aggiornamento disponibile** notifica potrebbe non essere visualizzati nel portale di amministrazione di Azure Stack. Per installare l'aggiornamento di Microsoft, scaricare e importarlo manualmente seguendo le istruzioni contenute in questo caso [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Passaggi di post-aggiornamento

*Non sono previsti passaggi di post-aggiornamento per aggiornamento 1809.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - [Link to KB]()  
 -->

## <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

Di seguito sono problemi noti di post-installazione per questa versione di build.

### <a name="portal"></a>Portale

- La documentazione tecnica di Azure Stack è incentrato sulla versione più recente. A causa di un portale modifiche tra le versioni, ciò che viene visualizzato quando si utilizzano i portali di Azure Stack può variare da quelli visualizzati nella documentazione.

<!-- 2930718 - IS ASDK --> 
- Nel portale di amministrazione, quando si accede ai dettagli di qualsiasi sottoscrizione utente, dopo aver chiuso il pannello e facendo clic su **recenti**, non viene visualizzato il nome della sottoscrizione utente.

<!-- 3060156 - IS ASDK --> 
- Nei portali di amministratore e utente, facendo clic sulle impostazioni del portale e selezionando **Elimina tutte le impostazioni e i dashboard privati** non funziona come previsto. Viene visualizzata una notifica di errore. 

<!-- 2930799 - IS ASDK --> 
- Nei portali di amministratore e utente, sotto **tutti i servizi**, l'asset **piani di protezione DDoS** è elencato in modo non corretto. Non è disponibile in Azure Stack. Se si tenta di crearla, viene visualizzato un errore che informa che il portale non è stato possibile creare l'elemento del marketplace. 

<!-- 2930820 - IS ASDK --> 
- Nei portali di amministratore e utente, se si cerca "Docker", l'elemento viene restituito in modo non corretto. Non è disponibile in Azure Stack. Se si tenta di crearla, viene visualizzato un pannello con l'indicazione di errore. 

<!-- 2967387 – IS, ASDK --> 
- L'account usato per accedere al portale di amministratore o utente di Azure Stack viene visualizzato come **utente non identificato**. Questo messaggio viene visualizzato quando l'account non dispone di un *primo* o *ultimo* nome specificato. Per risolvere questo problema, modificare l'account utente per fornire il primo o ultimo nome. È necessario quindi disconnettersi e quindi accedere al portale.  

<!--  2873083 - IS ASDK --> 
-  Quando si usa il portale per creare un set di scalabilità di macchine virtuali (VMSS), di impostare il *dimensioni istanze* elenco a discesa non viene caricato correttamente quando si usa Internet Explorer. Per risolvere questo problema, usare un altro browser quando si usa il portale per creare un set di scalabilità.  

<!-- 2931230 – IS  ASDK --> 
- Impossibile eliminare i piani che vengono aggiunti a una sottoscrizione utente come un piano del componente aggiuntivo, anche quando si rimuove il piano dalla sottoscrizione utente. Il piano rimarrà fino a quando non vengono eliminate anche le sottoscrizioni che fanno riferimento il piano aggiuntivo. 

<!--2760466 – IS  ASDK --> 
- Quando si installa un nuovo ambiente di Azure Stack che esegue questa versione, l'avviso che indica *attivazione obbligatoria* potrebbe non essere visualizzato. [Attivazione](azure-stack-registration.md) è necessario prima di poter usare diffusione di marketplace.  

<!-- TBD - IS ASDK --> 
- I due tipi di sottoscrizione amministrativi che sono state introdotte con la versione 1804 non devono essere utilizzati. I tipi di sottoscrizione siano **sottoscrizione di misurazione**, e **sottoscrizione consumo**. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il **Provider predefinito** tipo di sottoscrizione.

<!-- TBD - IS ASDK --> 
- Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

<!-- TBD - IS ASDK --> 
- Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.


### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e

<!-- TBD - IS -->
- Vengono visualizzati più volte e quindi non vengono più visualizzati nel sistema Azure Stack, è possibile visualizzare gli avvisi seguenti:
   - *Istanza del ruolo di infrastruttura non disponibile*
   - *Il nodo di unità di scala è offline*
   
  Eseguire la [Test-AzureStack](azure-stack-diagnostic-test.md) cmdlet per verificare l'integrità delle istanze del ruolo di infrastruttura e ridimensionare i nodi di unità. Se viene rilevato alcun problema dal [Test-AzureStack](azure-stack-diagnostic-test.md), è possibile ignorare questi avvisi. Se viene rilevato un problema, è possibile provare ad avviare l'istanza del ruolo di infrastruttura o un nodo tramite il portale di amministrazione o PowerShell.

<!-- 1264761 - IS ASDK -->  
- Si potrebbero visualizzare avvisi per i **controller integrità** componente con i dettagli seguenti:  

   Avviso #1:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità Heartbeat Scanner è disponibile. Ciò può influire sulle metriche e i report sull'integrità.  

  Avviso #2:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità errore Scanner è disponibile. Ciò può influire sulle metriche e i report sull'integrità.

  Entrambi gli avvisi possono essere tranquillamente ignorati e si chiude automaticamente nel corso del tempo.  


<!-- 2812138 | IS --> 
- Si potrebbe essere visualizzato un avviso per il **archiviazione** componente che contiene i dettagli seguenti:

   - NOME: Errore di comunicazione interna del servizio di archiviazione  
   - GRAVITÀ: critico  
   - COMPONENTE: archiviazione  
   - Descrizione: Errore di comunicazione interna del servizio di archiviazione si è verificato durante l'invio di richieste per i nodi seguenti.  

    L'avviso può essere tranquillamente ignorato, ma è necessario chiudere l'avviso manualmente.

<!-- 2368581 - IS, ASDK --> 
- Un operatore di Azure Stack, se si riceve un avviso di memoria insufficiente e le macchine virtuali tenant non riuscire a distribuire con un **errore di creazione della macchina virtuale di Fabric**, è possibile che l'indicatore di Azure Stack ha esaurito la memoria disponibile. Usare la [Capacity Planner di Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) per comprendere meglio la capacità disponibile per i carichi di lavoro.

### <a name="compute"></a>Calcolo

<!-- 3235634 – IS, ASDK -->
- Per distribuire le macchine virtuali con dimensioni che contiene un **v2** suffisso; ad esempio, **Standard_A2_v2**, specificare il suffisso come **Standard_A2_v2** (v lettere minuscole). Non utilizzare **Standard_A2_V2** (lettere maiuscole V). Ciò funziona in Azure globale ed è un'incoerenza nello Stack di Azure.

<!-- 3099544 – IS, ASDK --> 
- Quando si crea una nuova macchina virtuale (VM) tramite il portale di Azure Stack, e si seleziona la dimensione di macchina virtuale, la colonna dollari/mese viene visualizzata con un **disponibile** messaggio. Questa colonna non deve essere visualizzato; visualizzare la macchina virtuale dei prezzi di colonna non è supportato in Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Quando si usa la [ **Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), è necessario usare il **- OsUri** parametro come URI in cui è stato caricato il disco dell'account di archiviazione. Se si usa il percorso locale del disco, il cmdlet non riesce con l'errore seguente: *operazione a esecuzione prolungata non è riuscita con stato 'Non riuscita'*. 

<!--  2795678 – IS, ASDK --> 
- Quando si usa il portale per creare macchine virtuali (VM) in una dimensione di VM premium (DS, Ds_v2, ADFS, FSv2), viene creata la macchina virtuale in un account di archiviazione standard. La creazione di un account di archiviazione standard non influisce sul livello funzionale, numero di IOPs, o fatturazione. 

   È possibile ignorare l'avviso con la dicitura: *si è scelto di usare un disco standard in una dimensione che supporta i dischi premium. Ciò potrebbe influire sulle prestazioni del sistema operativo e non è consigliato. Provare a utilizzare archiviazione premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- L'esperienza di creazione macchina virtuale (VMSS) set di scalabilità offre basata su CentOS 7.2 come un'opzione per la distribuzione. Poiché tale immagine non è disponibile in Azure Stack, selezionare un altro sistema operativo per la distribuzione o usare un modello di Azure Resource Manager specificando un'altra immagine CentOS che è stata scaricata prima della distribuzione dal marketplace dall'operatore.  

<!-- 2724873 - IS --> 
- Quando si usano i cmdlet di PowerShell **Start-AzsScaleUnitNode** oppure **Stop-AzsScaleunitNode** per gestire le unità di scala, il primo tentativo di avviare o arrestare l'unità di scala potrebbe non riuscire. Se il cmdlet non riesce durante la prima esecuzione, eseguire il cmdlet una seconda volta. La seconda esecuzione avrà esito positivo per completare l'operazione. 

<!-- TBD - IS ASDK --> 
- Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

<!-- 1662991 IS ASDK --> 
- Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.  

<!-- 2724961- IS ASDK --> 
- Quando si registra il **Microsoft.Insight** provider di risorse nelle impostazioni di sottoscrizione e creare una VM Windows con Guest OS diagnostica abilitata, il grafico della percentuale di CPU nella pagina di panoramica della macchina virtuale non mostra i dati delle metriche.

   Per trovare i dati delle metriche, ad esempio il grafico della percentuale di CPU per la macchina virtuale, passare alla finestra di metriche e visualizzare tutte le metriche guest macchina virtuale di Windows supportate.



### <a name="networking"></a>Rete  

<!-- 1766332 - IS ASDK --> 
- Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

<!-- 16309153 - IS ASDK --> 
- In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

<!-- 2529607 - IS ASDK --> 
- Durante l'Azure Stack *rotazione segreta*, è previsto un periodo in cui gli indirizzi IP pubblici non sono raggiungibili da due a cinque minuti.

<!-- 2664148 - IS ASDK --> 
-   Negli scenari in cui il tenant è l'accesso alle proprie macchine virtuali tramite un tunnel VPN S2S, si può verificare uno scenario in cui i tentativi di connessione non riuscire se la subnet locale è stata aggiunta al Gateway di rete locale dopo gateway è già stato creato. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>Servizio app

<!-- 2352906 - IS ASDK --> 
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.


### <a name="usage"></a>Uso  

<!-- TBD - IS ASDK --> 
- I dati di contatore sull'utilizzo di indirizzi IP pubblici viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1809 dal [qui](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Passaggi successivi

- Per esaminare i criteri di manutenzione per i sistemi integrati di Azure Stack e le operazioni da eseguire per mantenere il sistema in uno stato supportato, vedere [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).  
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).  
- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).  
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).  
