---
title: Aggiornamento di Azure Stack 1807 | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 1807 per i sistemi integrati di Azure Stack, inclusi i problemi noti e su dove scaricare l'aggiornamento.
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: bde8ea74d2b6d36cd070598d0542e5be4bdef244
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816175"
---
# <a name="azure-stack-1807-update"></a>Aggiornamento di Azure Stack 1807

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo descrive il contenuto del pacchetto di aggiornamento 1807. Questo aggiornamento include miglioramenti e correzioni di problemi noti per questa versione di Azure Stack e dove scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]  
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione

È il numero di build di aggiornamento di Azure Stack 1807 **1.1807.0.76**.  

### <a name="new-features"></a>Nuove funzionalità

Questo aggiornamento include i miglioramenti seguenti per Azure Stack.

<!-- 1658937 | ASDK, IS --> 
- **Avviare i backup in una pianificazione predefinita** -come appliance, Azure Stack possono ora attiva automaticamente i backup infrastructure periodicamente per evitare l'intervento dell'utente. Azure Stack automaticamente anche consentirà di pulire la condivisione esterna per i backup precedenti rispetto al periodo di memorizzazione definito. Per altre informazioni, vedere [abilitare il Backup per Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Nel tempo di backup totale tempo di trasferimento dei dati aggiunti.** Per altre informazioni, vedere [abilitare il Backup per Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS -->  
- **Backup esterna capacità Mostra ora la capacità corretta della condivisione esterna.** (In precedenza questo era hardcoded a 10 GB.) Per altre informazioni, vedere [abilitare il Backup per Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

<!-- 2508488 |  IS   -->
- **Espandere la capacità** dal [aggiunta di nodi di unità di scalabilità aggiuntiva](azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **Modelli di Resource Manager di Azure supportano ora l'elemento di condizione** -è ora possibile distribuire una risorsa in un modello di gestione risorse di Azure usando una condizione. È possibile progettare il modello per distribuire una risorsa basata su una condizione, ad esempio determinare se è presente un valore di parametro. Per informazioni sull'utilizzo di un modello come una condizione, vedere [distribuire in modo condizionale una risorsa](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) e [sezione Variables dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) nella documentazione di Azure. 

   È anche possibile usare i modelli per [distribuire risorse in più di una sottoscrizione o gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **È stato aggiornato il supporto della versione API di Microsoft. Network resource** per includere il supporto per l'API versione 2017-10-01 da 2015-06-15 per le risorse di rete di Azure Stack.  Supporto per le versioni di risorse tra 15-06-2015 e 2017-10-01 non è incluso in questa versione.  Consultare [considerazioni per la rete di Azure Stack](user/azure-stack-network-differences.md) le differenze di funzionalità.

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack ha aggiunto il supporto per le ricerche DNS inverse per con connessione esterna endpoint dell'infrastruttura di Azure Stack** (che è per portale, adminportal, gestione e adminmanagement). In questo modo i nomi degli endpoint esterni di Azure Stack essere risolto da un indirizzo IP.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack supporta ora l'aggiunta di interfacce di rete aggiuntiva in una VM esistente.**  Questa funzionalità è disponibile tramite il portale, PowerShell e CLI. Per altre informazioni, vedere [aggiungere o rimuovere interfacce di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) nella documentazione di Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Sono stati apportati miglioramenti nell'accuratezza e la resilienza per i contatori di utilizzo di rete**.  Contatori di utilizzo di rete sono ora più accurati e prendono in sottoscrizioni account sospeso, i periodi di interruzione e le race condition.

<!-- 2753080 | IS -->  
- **Aggiornare notifiche disponibili.** Connesso Azure Stack le distribuzioni verranno ora verificare periodicamente un endpoint protetto e determinare se un aggiornamento è disponibile per il cloud. Questa notifica viene visualizzata nel riquadro aggiornamenti, come verrebbe mostrata dopo cercando manualmente e l'importazione di un nuovo aggiornamento. Altre informazioni, vedere [gestione degli aggiornamenti per Azure Stack](azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **Miglioramenti al client di syslog (funzionalità di anteprima) di Azure Stack**. Questa consente l'inoltro di controllo e i log relativi all'infrastruttura di Azure Stack a un syslog server o della sicurezza le informazioni ed eventi (SIEM) software di gestione esterno ad Azure Stack. Il client di syslog supporta ora il protocollo TCP con testo normale o la crittografia TLS 1.2, il secondo è la configurazione predefinita. È possibile configurare la connessione TLS con l'autenticazione solo server o reciproco.

  Per configurare la modalità di comunicazione client syslog (ad esempio protocollo, la crittografia e autenticazione) con il server syslog, usare il *Set-SyslogServer* cmdlet. Questo cmdlet è disponibile dall'endpoint con privilegi (PEP).

  Per aggiungere il certificato sul lato client per l'autenticazione reciproca del client TLS 1.2 syslog, usare il cmdlet Set-SyslogClient nel PEP.

  Con questa versione di anteprima, è possibile visualizzare un numero molto maggiore di controlli e avvisi. 

  Poiché questa funzionalità è ancora in anteprima, non affidarsi a essa in ambienti di produzione.

  Per altre informazioni, vedere [l'inoltro di syslog di Azure Stack](azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **Azure Resource Manager include il nome dell'area.** Con questa versione, gli oggetti recuperati da Azure Resource Manager include ora l'attributo del nome di area. Se uno script di PowerShell esistente, l'oggetto viene passato direttamente a un altro cmdlet, lo script potrebbe produrre un errore e non riuscire. Questo è il comportamento conforme di Azure Resource Manager e richiede che il client chiama da cui sottrarre l'attributo di area. Per altre informazioni su Azure Resource Manager vedere [documentazione di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/). Verificare 8-10 mdb-->

<!-- TBD | IS, ASDK -->  
- **Modifiche apportate alle funzionalità di delega provider.** Partire 1807 i provider delegata modello è semplificato al fine di una migliore allineamento con il modello rivenditore di Azure e i provider di delegato non sarà in grado di creare altri provider delegata, essenzialmente rendere flat il modello e rendendo il Provider delegata funzionalità disponibili in un unico livello. Per abilitare la transizione al nuovo modello e la gestione delle sottoscrizioni, le sottoscrizioni utente ora possono essere spostate tra le sottoscrizioni del Provider delegata di nuovo o esistente che appartengono allo stesso tenant di Directory. È inoltre possibile spostare sottoscrizioni utente appartenenti alla sottoscrizione Provider predefinito per le sottoscrizioni del Provider delegata nello stesso tenant di Directory.  Per altre informazioni, vedere [delegare offerte in Azure Stack](azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Migliorata l'ora di creazione della macchina virtuale** per le macchine virtuali che vengono create con immagini Scarica dal marketplace di Azure.

<!-- TBD | IS, ASDK -->  
- **Miglioramenti all'usabilità di Azure Stack Capacity Planner**. Azure Stack [Capacity Planner](http://aka.ms/azstackcapacityplanner) offre ora un'esperienza semplificata per l'immissione di cache S2D e la capacità di S2D durante la definizione di soluzione gli SKU. È stato rimosso il limite di 1000 macchine Virtuali.


### <a name="fixed-issues"></a>Problemi risolti

<!-- TBD | ASDK, IS --> 
- Il processo di aggiornamento sono stati apportati miglioramenti vari per renderlo più affidabile. Inoltre, le correzioni sono state apportate all'infrastruttura sottostante, cui ridurre al minimo eventuali tempi di inattività per i carichi di lavoro durante l'aggiornamento.

<!--2292271 | ASDK, IS --> 
- È stato risolto un problema in cui un limite di Quota modificato non riguarda le sottoscrizioni esistenti. A questo punto, quando si aumenta un limite di Quota per una risorsa di rete che fa parte di un'offerta e piano associato a una sottoscrizione utente, il nuovo limite si applica a sottoscrizioni esistenti, nonché le nuove sottoscrizioni.

<!-- 448955 | IS ASDK --> 
- È possibile ora correttamente le query in log attività per i sistemi distribuiti in un fuso orario UTC + N.    

<!-- 2319627 |  ASDK, IS --> 
- Verifica preliminare per i parametri di configurazione del backup (chiave di crittografia/percorso/nome utente/Password) non è più imposta impostazioni non corrette per la configurazione del backup. (In precedenza, non corretta delle impostazioni sono state impostate nel backup e backup avrà quindi esito negativo quando attivato).

<!-- 2215948 |  ASDK, IS -->
- A questo punto l'elenco dei backup viene aggiornato quando si elimina manualmente il backup dalla condivisione esterna.

<!-- 2332636 | IS -->  
- Aggiornamento a questa versione non è più Reimposta il proprietario predefinito della sottoscrizione del provider predefinito per l'elemento predefinito **CloudAdmin** utente se distribuito con AD FS.

<!-- 2360715 |  ASDK, IS -->  
- Quando si configura integrazione di Data Center, è accedere non è più il file di metadati di AD FS da una condivisione file di Azure. Per altre informazioni, vedere [impostazione dell'integrazione di AD FS, fornendo il file di metadati di federazione](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- È stato risolto un problema che ha impedito agli utenti di assegnato un indirizzo IP pubblico esistente che è stata precedentemente assegnato a un'interfaccia di rete o servizio di bilanciamento del carico a una nuova interfaccia di rete o servizio di bilanciamento del carico.  

<!-- 2551834 - IS, ASDK --> 
- Quando si seleziona *Overview* per un account di archiviazione nei portali di amministratore o utente, il *Essentials* riquadro ora consente di visualizzare tutte le informazioni previste correttamente. 

<!-- 2551834 - IS, ASDK --> 
- Quando si seleziona *tag* per un account di archiviazione nei portali di amministratore o utente, le informazioni ora visualizza correttamente.

<!-- TBD - IS ASDK --> 
- Questa versione di Azure Stack consente di correggere il problema che impediva l'applicazione degli aggiornamenti di driver dai pacchetti di estensione OEM.

<!-- 2055809- IS ASDK --> 
- È stato risolto un problema che si è autorizzato a eliminare le macchine virtuali nel pannello calcolo quando non viene creata la macchina virtuale.  

<!--  2643962 IS ASDK -->  
- Avviso relativo alla *bassa capacità di memoria* non viene più visualizzata in modo non corretto.

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures
Stack di Azure Usa le installazioni Server Core di Windows Server 2016 all'infrastruttura a chiave host. Questa versione consente di installare gli aggiornamenti di Windows Server 2016 seguenti nei server dell'infrastruttura per Azure Stack: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Per altre informazioni su questi problemi di protezione, fare clic su collegamenti precedenti o vedere gli articoli della Microsoft Knowledge Base [4338814](https://support.microsoft.com/help/4338814) e [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

- Installare Azure Stack [1805 aggiornare](azure-stack-update-1805.md) prima di applicare l'aggiornamento di Azure Stack 1807.  Si è verificato alcun aggiornamento 1806.  

- Installare la versione più recente disponibile [update o un hotfix per la versione 1805](azure-stack-update-1805.md#post-update-steps).  
  > [!TIP]  
  > Sottoscrivere seguenti *RRS* oppure *Atom* i feed di stare al passo con gli aggiornamenti rapidi di Azure Stack:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con i parametri seguenti per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

<!-- 2468613 - IS --> 
- Durante l'installazione di questo aggiornamento, si potrebbero visualizzare avvisi con il titolo *Error: modello per FaultType UserAccounts.New è manca.*  È possibile ignorare questi avvisi. Questi avvisi chiuderà automaticamente al termine dell'installazione dell'aggiornamento.

<!-- 2489559 - IS --> 
- Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per altre informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).

<!-- 2830461 - IS --> 
- In determinate circostanze quando un aggiornamento richiede attenzione, l'avviso corrispondente potrebbe non essere generato. Uno stato accurato comunque si rifletteranno nel portale e non è interessato.

### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
Dopo l'installazione di questo aggiornamento, installare eventuali hotfix applicabili. Per altre informazioni, visualizzare i seguenti articoli della knowledge base, nonché nostri [criteri per la manutenzione](azure-stack-servicing-policy.md). 
- [KB 4464231 – Azure Stack hotfix per Azure Stack Hotfix 1.1807.1.78](https://support.microsoft.com/help/4464231)

<!-- 2933866 – IS --> Dopo l'installazione di questo aggiornamento, è possibile visualizzare **migliorato lo stato per le installazioni di aggiornamento non riuscito.** Potrebbe trattarsi di informazioni sugli errori di installazione aggiornamento precedente che sono soggette a revisione in modo da riflettere le due nuove categorie di stato. Le nuove categorie di stato vengono *PreparationFailed*, e *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

Di seguito sono problemi noti di post-installazione per questa versione di build.

### <a name="portal"></a>Portale

- La documentazione tecnica di Azure Stack è incentrato sulla versione più recente. A causa di un portale modifiche tra le versioni, ciò che viene visualizzato quando si utilizzano i portali di Azure Stack può variare da quelli visualizzati nella documentazione. 

- La possibilità [aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) entro l'amministratore del portale non è disponibile. Per Azure Stack i sistemi integrati, usare invece il collegamento seguente: [ https://aka.ms/newsupportrequest ](https://aka.ms/newsupportrequest).

<!-- 2931230 – IS  ASDK --> 
- Impossibile eliminare i piani che vengono aggiunti a una sottoscrizione utente come un piano del componente aggiuntivo, anche quando si rimuove il piano dalla sottoscrizione utente. Il piano rimarrà fino a quando non vengono eliminate anche le sottoscrizioni che fanno riferimento il piano aggiuntivo. 

<!--2760466 – IS  ASDK --> 
- Quando si installa un nuovo ambiente di Azure Stack che esegue questa versione, l'avviso che indica *attivazione obbligatoria* potrebbe non essere visualizzato. [Attivazione](azure-stack-registration.md) è necessario prima di poter usare diffusione di marketplace.  

<!-- TBD - IS ASDK --> 
- I due tipi di sottoscrizione amministrativo che erano [introdotta con la versione 1804](azure-stack-update-1804.md#new-features) non deve essere utilizzato. I tipi di sottoscrizione siano **sottoscrizione di misurazione**, e **sottoscrizione consumo**. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il **Provider predefinito** tipo di sottoscrizione.

<!-- 2403291 - IS ASDK --> 
- Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore di portali di amministratore e utente. Se non è possibile accedere la barra di scorrimento orizzontale, usare i percorsi di navigazione per passare al pannello precedente nel portale selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.

  ![Barra di navigazione](media/azure-stack-update-1804/breadcrumb.png)

<!-- TBD - IS --> 
- Potrebbe non essere possibile visualizzare le risorse di calcolo o archiviazione nel portale di amministrazione. La causa del problema è un errore durante l'installazione dell'aggiornamento che causa l'aggiornamento da segnalare in modo errato come esito positivo. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

<!-- TBD - IS --> 
- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona a forma di ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

<!-- TBD - IS ASDK --> 
- Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

<!-- TBD - IS ASDK --> 
- Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.


### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
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
- Si potrebbe essere visualizzato un avviso per **archiviazione** componente con i dettagli seguenti:

   - NOME: Errore di comunicazione interna del servizio di archiviazione  
   - GRAVITÀ: critico  
   - COMPONENTE: archiviazione  
   - Descrizione: Errore di comunicazione interna del servizio di archiviazione si è verificato durante l'invio di richieste per i nodi seguenti.  

    L'avviso può essere tranquillamente ignorato, ma è necessario chiudere l'avviso manualmente.

<!-- 2368581 - IS. ASDK --> 
- Un operatore di Azure Stack, se si riceve un avviso di memoria insufficiente e le macchine virtuali tenant non riuscire a distribuire con un **errore di creazione della macchina virtuale di Fabric**, è possibile che l'indicatore di Azure Stack ha esaurito la memoria disponibile. Usare la [Capacity Planner di Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) per comprendere meglio la capacità disponibile per i carichi di lavoro.


### <a name="compute"></a>Calcolo

<!-- 2724873 - IS --> 
- Quando si usano i cmdlet di PowerShell **Start-AzsScaleUnitNode** oppure **Stop-AzsScaleunitNode** per gestire le unità di scala, il primo tentativo di avviare o arrestare l'unità di scala potrebbe non riuscire. Se il cmdlet non riesce durante la prima esecuzione, eseguire il cmdlet una seconda volta. La seconda esecuzione avrà esito positivo per completare l'operazione. 

<!-- 2494144 - IS, ASDK --> 
- Quando si seleziona una dimensione di macchina virtuale per una distribuzione della macchina virtuale, alcune dimensioni di VM serie F non sono visibili nell'ambito del selettore di dimensione quando si crea una macchina virtuale. Le dimensioni delle macchine Virtuali seguenti non vengono visualizzati nel selettore: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  In alternativa, usare uno dei metodi seguenti per distribuire una macchina virtuale. In ogni metodo, è necessario specificare le dimensioni di macchina virtuale da usare.

  - **Modello di Azure Resource Manager:** quando si usa un modello, impostare il *vmSize* nel modello per uguale alle dimensioni della macchina virtuale da usare. Ad esempio, la voce seguente viene usata per distribuire una macchina virtuale che usa il *F32s_v2* dimensioni:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Comando di Azure:** è possibile usare il [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e specificare le dimensioni VM come parametro, simile a `--size "Standard_F32s_v2"`.

  - **PowerShell:** con PowerShell è possibile usare [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) con il parametro che specifica le dimensioni VM, simile a `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

<!-- TBD - IS --> 
- Quando si crea un set di disponibilità nel portale, dovrà **New** > **calcolo** > **set di disponibilità**, è possibile creare solo un set di disponibilità con un dominio di errore e dominio di aggiornamento 1. Creare set tramite PowerShell, interfaccia della riga di comando, o dall'interno di disponibilità come soluzione alternativa, quando si crea una nuova macchina virtuale, il portale.

<!-- TBD - IS ASDK --> 
- Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che è possibile collegare a una VM serie DS. Le VM serie DS può supportare tutti i dischi dati come la configurazione di Azure.

<!-- TBD - IS ASDK --> 
- Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

<!-- 1662991 IS ASDK --> 
- Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.  

<!-- 2724961- IS ASDK --> 
- Quando si registra il **Microsoft.Insight** provider di risorse nelle impostazioni di sottoscrizione e creare una VM Windows con Guest OS diagnostica abilitata, la pagina di panoramica della macchina virtuale non mostra i dati delle metriche. 

   Per trovare i dati delle metriche, ad esempio il grafico della percentuale di CPU per la macchina virtuale, passare al **metriche** pannello e Mostra tutte le macchine Virtuali di Windows supportate guest metriche.

### <a name="networking"></a>Rete  

<!-- 1766332 - IS ASDK --> 
- Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

<!-- 16309153 - IS ASDK --> 
- In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

<!-- 2702741 -  IS ASDK --> 
- Non è garantito che gli indirizzi IP pubblici che vengono distribuiti tramite il metodo di allocazione dinamica mantenute dopo l'esecuzione di un arresto-deallocazione.

<!-- 2529607 - IS ASDK --> 
- Durante l'Azure Stack *rotazione segreta*, è previsto un periodo in cui gli indirizzi IP pubblici non sono raggiungibili da due a cinque minuti.

<!-- 2664148 - IS ASDK --> 
- Negli scenari in cui il tenant è l'accesso alle proprie macchine virtuali tramite un tunnel VPN S2S, si può verificare uno scenario in cui i tentativi di connessione non riuscire se la subnet locale è stata aggiunta al Gateway di rete locale dopo gateway è già stato creato. 


### <a name="sql-and-mysql"></a>SQL e MySQL



<!-- No Number - IS, ASDK -->  
- Caratteri speciali, tra cui spazi e i periodi, non sono supportati nel **famiglia** nome quando si crea uno SKU per i provider di risorse SQL e MySQL. 

<!-- TBD - IS --> 
- È supportato solo il provider di risorse per creare gli elementi nei server di tale host SQL o MySQL. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.  

<!-- TBD - IS -->
> [!NOTE]   
> Dopo l'aggiornamento a questa versione di Azure Stack, è possibile continuare a usare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare SQL e MySQL quando diventa disponibile una nuova versione. Come Azure Stack, applicare gli aggiornamenti per i provider di risorse SQL e MySQL in modo sequenziale. Ad esempio, se si usa la versione 1804, applicare prima versione 1805 e quindi aggiornare a 1807.  
>  
> L'installazione di questo aggiornamento non influenza l'utilizzo corrente di provider di risorse SQL o MySQL dagli utenti. 
> Indipendentemente dalla versione dei provider di risorse che è usare, i dati degli utenti nei relativi database non viene aggiornati e rimangono accessibili.  

### <a name="app-service"></a>Servizio app

<!-- 2352906 - IS ASDK --> 
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

<!-- 2489178 - IS ASDK --> 
- Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.

<!-- TBD - IS ASDK --> 
- Servizio App può essere distribuito solo nel *sottoscrizione del Provider predefinito* in questo momento. 


### <a name="usage"></a>Uso  
<!-- TBD - IS ASDK --> 
- Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1807 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="next-steps"></a>Passaggi successivi
- Per esaminare i criteri di manutenzione per i sistemi integrati di Azure Stack e le operazioni da eseguire per mantenere il sistema in uno stato supportato, vedere [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).  
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).  
- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).  
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).  
