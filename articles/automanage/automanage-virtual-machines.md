---
title: Gestione automatici di Azure per le macchine virtuali
description: Informazioni su gestione automatici di Azure per le macchine virtuali.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 8e48a5c896c4927b82f7d77f31b7f1c47fd156c5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940863"
---
# <a name="azure-automanage-for-virtual-machines"></a>Gestione automatici di Azure per le macchine virtuali

Questo articolo illustra le informazioni relative a gestione automatici di Azure per le macchine virtuali, che offrono i vantaggi seguenti:

- Carica in modo intelligente le macchine virtuali per selezionare i servizi di Azure per le procedure consigliate
- Configura automaticamente ogni servizio per le procedure consigliate di Azure
- Esegue il monitoraggio della deviazione e corregge il controllo quando viene rilevato
- Fornisce un'esperienza semplice (punto, fare clic, impostare, dimenticare)


## <a name="overview"></a>Panoramica

Gestione automatica di Azure per le macchine virtuali è un servizio che elimina la necessità di individuare, sapere come eseguire l'onboarding e come configurare determinati servizi in Azure che potrebbero trarre vantaggio dalla macchina virtuale. Questi servizi consentono di migliorare l'affidabilità, la sicurezza e la gestione per le macchine virtuali e sono considerati servizi di procedure consigliate di Azure, ad esempio [azure gestione aggiornamenti](../automation/update-management/update-mgmt-overview.md) e [backup di Azure](../backup/backup-overview.md) , solo per citarne alcuni.

Dopo l'onboarding delle macchine virtuali in gestione automatica di Azure, ogni servizio Best Practice viene configurato automaticamente sulle impostazioni consigliate. Le procedure consigliate sono diverse per ogni servizio. Un esempio potrebbe essere backup di Azure, in cui la procedura consigliata potrebbe consistere nel eseguire il backup della macchina virtuale una volta al giorno e avere un periodo di conservazione di sei mesi.

Azure automanage monitora automaticamente anche la deriva e corregge il controllo per l'it quando viene rilevato. Ciò significa che se la macchina virtuale viene caricata in gestione automatica di Azure, la configurazione non viene configurata solo per le procedure consigliate di Azure, ma il computer viene monitorato per assicurarsi che continui a conformarsi a tali procedure consigliate nell'intero ciclo di vita. Se la macchina virtuale si sposta o devia da queste procedure, la correzione verrà corretta e il computer tornerà nello stato desiderato.

Infine, l'esperienza è incredibilmente semplice.


## <a name="prerequisites"></a>Prerequisiti

È necessario prendere in considerazione diversi prerequisiti prima di provare ad abilitare la gestione automatici di Azure nelle macchine virtuali.

- Solo macchine virtuali Windows Server
- Le macchine virtuali devono essere in esecuzione
- Solo macchine virtuali con set di scalabilità
- Le macchine virtuali devono trovarsi in un'area supportata
- L'utente deve disporre delle autorizzazioni corrette
- Le macchine virtuali non devono essere collegate a un'area di lavoro di log Analytics in una sottoscrizione diversa

Per abilitare la gestione, è necessaria l'autorizzazione RBAC seguente: ruolo **proprietario** o **collaboratore** insieme ai ruoli di **amministratore accesso utenti** .

È anche importante notare che automanage supporta solo le macchine virtuali Windows situate nelle aree seguenti: Europa occidentale, Stati Uniti orientali, Stati Uniti occidentali 2, Canada centrale, Stati Uniti centro-occidentali.

## <a name="participating-services"></a>Servizi partecipanti

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Onboarding intelligente dei servizi.":::

Vedere [gestione automatica di Azure per le procedure consigliate per le macchine virtuali](virtual-machines-best-practices.md) per l'elenco completo dei servizi di Azure partecipanti, oltre ai profili di configurazione supportati.

 L'utente verrà automaticamente caricato in questi servizi partecipanti. Sono essenziali per le procedure consigliate white paper, che è possibile trovare nel [Framework di adozione del cloud](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management).

Per tutti questi servizi, verrà eseguito automaticamente il caricamento, la configurazione automatica, il monitoraggio della deviazione e la mediazione se viene rilevata la deriva.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Abilitazione della gestione per le macchine virtuali in portale di Azure

Nella portale di Azure è possibile abilitare la gestione automatica in una macchina virtuale esistente o quando si crea una nuova macchina virtuale. Per i passaggi concisi di questo processo, vedere la [Guida introduttiva alla gestione delle macchine virtuali](quick-create-virtual-machines-portal.md).

Se è la prima volta che si Abilita la gestione automatica per la macchina virtuale, è possibile eseguire la ricerca nella portale di Azure per le **procedure consigliate per la gestione automatica-macchine virtuali di Azure**. Fare clic su **Abilita in una macchina virtuale esistente**, selezionare le macchine virtuali da caricare, fare clic su **Seleziona**, **Abilita**e termina.

L'unica volta in cui potrebbe essere necessario interagire con questa macchina virtuale per gestire questi servizi si trova nel caso in cui si sia tentato di correggere la macchina virtuale, ma non è stato possibile eseguire questa operazione. Se la macchina virtuale è stata risolta correttamente, verrà ripristinata la conformità senza avvisare dell'utente.


## <a name="configuration-profiles"></a>Profili di configurazione

Quando si Abilita la gestione automatica per la macchina virtuale, è necessario un profilo di configurazione. I profili di configurazione sono la base di questo servizio. Definiscono esattamente i servizi in cui si caricano i computer e in qualche misura la configurazione di tali servizi.

### <a name="default-configuration-profiles"></a>Profili di configurazione predefiniti

Sono attualmente disponibili due profili di configurazione.

- **Procedure consigliate per macchine virtuali di Azure:** il profilo di configurazione di sviluppo/test è progettato per i computer di sviluppo/test.
- **Procedure consigliate per le macchine virtuali di Azure:** il profilo di configurazione di produzione è per la produzione.

Il motivo di questo differenziatore è dovuto al fatto che alcuni servizi sono consigliati in base al carico di lavoro in esecuzione. Ad esempio, in un computer di produzione verrà automaticamente caricato in backup di Azure. Tuttavia, per un computer di sviluppo/test, un servizio di backup potrebbe essere un costo superfluo, poiché i computer di sviluppo/test sono in genere un impatto aziendale inferiore.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Personalizzazione di un profilo di configurazione usando le preferenze

Oltre ai servizi standard di cui si esegue l'onboarding, è possibile configurare un certo subset di preferenze. Queste preferenze sono consentite in un'ampia gamma di opzioni di configurazione che non violano le procedure consigliate. Ad esempio, nel caso di backup di Azure, sarà possibile definire la frequenza del backup e il giorno della settimana in cui si verifica. Tuttavia, *non* sarà possibile disattivare completamente backup di Azure.

> [!NOTE]
> Nel profilo di configurazione di sviluppo/test non è possibile eseguire il backup della macchina virtuale.

È possibile modificare le impostazioni di un profilo di configurazione predefinito tramite le preferenze. Per informazioni su come creare una preferenza, vedere [qui](virtual-machines-custom-preferences.md).

> [!NOTE]
> Non è possibile modificare il profilo di configurazione nella macchina virtuale mentre è abilitata la funzionalità automanage. È necessario disabilitare la gestione automatica per tale macchina virtuale e quindi abilitare di nuovo la gestione automatica con il profilo di configurazione e le preferenze desiderate.


## <a name="automanage-account"></a>Gestisci account

L'account di gestione automatica è il contesto di sicurezza o l'identità con cui si verificano le operazioni automatiche. In genere, l'opzione account di gestione automatica non è necessaria per la selezione, ma se è presente uno scenario di delega in cui si vuole dividere la gestione automatica (ad esempio tra due amministratori di sistema), questa opzione consente di definire un'identità di Azure per ognuno di questi amministratori.

Nel portale di Azure esperienza, quando si Abilita la funzionalità di gestione delle macchine virtuali, è disponibile un elenco a discesa avanzate nel pannello **Abilita VM di Azure per le procedure consigliate** che consente di assegnare o creare manualmente l'account automanage.

> [!NOTE]
> Per abilitare la gestione, è necessaria l'autorizzazione RBAC seguente: ruolo **proprietario** o **collaboratore** insieme ai ruoli di **amministratore accesso utenti** .


## <a name="status-of-vms"></a>Stato delle macchine virtuali

Nella portale di Azure passare alla pagina Gestione automatica **-procedure consigliate per macchine virtuali di Azure** in cui sono elencate tutte le macchine virtuali gestite automaticamente. Qui viene visualizzato lo stato complessivo di ogni macchina virtuale.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Elenco delle macchine virtuali configurate.":::

Per ogni macchina virtuale elencata vengono visualizzati i dettagli seguenti: nome, profilo di configurazione, preferenza di configurazione, stato, account, sottoscrizione e gruppo di risorse.

Nella colonna **stato** possono essere visualizzati gli Stati seguenti:
- *In corso* : la macchina virtuale è stata appena abilitata e configurata
- *Configurato* : la macchina virtuale è configurata e non viene rilevata alcuna deviazione
- *Non riuscito* : la macchina virtuale è stata spostata e non è stato possibile correggere

Se lo **stato** viene visualizzato come *non riuscito*, è possibile risolvere i problemi di distribuzione tramite il gruppo di risorse in cui si trova la macchina virtuale. Passare a **gruppi di risorse**, selezionare il gruppo di risorse, fare clic su **distribuzioni** e vedere lo stato *non riuscito* insieme ai dettagli dell'errore.


## <a name="disabling-automanage-for-vms"></a>Disabilitazione di automanage per le macchine virtuali

È possibile decidere un giorno per disabilitare la gestione in determinate macchine virtuali. Ad esempio, nel computer è in esecuzione un carico di lavoro sicuro con privilegi avanzati ed è necessario bloccarlo ulteriormente rispetto ad Azure avrebbe eseguito naturalmente, quindi è necessario configurare il computer al di fuori delle procedure consigliate di Azure.

Per eseguire questa operazione nella portale di Azure, passare alla pagina **gestione automatica-procedure consigliate per macchine virtuali di Azure** in cui sono elencate tutte le macchine virtuali gestite automaticamente. Selezionare la casella di controllo accanto alla macchina virtuale che si vuole disabilitare da gestione automatica, quindi fare clic sul pulsante **Disabilita automanage** .

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Disabilitazione di automanage in una macchina virtuale.":::

Leggere attentamente la messaggistica nel popup risultante prima di accettare la **disabilitazione**.

```
Disabling automanagement in a VM results in the following behavior:

1.    The configuration of the VM and the services it's onboarded to will not be changed
2.    Any changes incurred by those services will remain billable and will continue to be incurred
3.    Any Automanage behaviors will stop immediately
```

Prima di tutto, la macchina virtuale non verrà disattivata da nessuno dei servizi che sono stati caricati e configurati. Tutti gli addebiti subiti da tali servizi continueranno a essere fatturabili. Se necessario, sarà necessario eseguire l'operazione di disattivazione. Qualsiasi comportamento di gestione di automanage viene interrotto immediatamente. Ad esempio, la macchina virtuale non sarà più monitorata per la deviazione.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso che la gestione automaticamente per le macchine virtuali offre un mezzo per il quale è possibile eliminare la necessità di sapere, caricare e configurare i servizi di Azure per le procedure consigliate. Inoltre, se un computer caricato per la gestione automatica per le macchine virtuali deriva dai profili di configurazione impostati, verrà ripristinato automaticamente in conformità.

Provare ad abilitare la gestione automatici per le macchine virtuali nella portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare la gestione automatici per le macchine virtuali nella portale di Azure](quick-create-virtual-machines-portal.md)
