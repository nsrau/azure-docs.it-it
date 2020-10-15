---
title: Distribuire host dedicati di Azure usando il portale di Azure
description: Distribuire le macchine virtuali e i set di scalabilità in host dedicati usando il portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/04/2020
ms.author: cynthn
ms.openlocfilehash: a6bef4944207e26f2de93daa89fa1418c5c44c4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91373154"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Distribuire macchine virtuali e set di scalabilità in host dedicati tramite il portale 

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 


> [!IMPORTANT]
> Questo articolo illustra anche il posizionamento automatico delle VM e delle istanze dei set di scalabilità. La selezione host automatica è attualmente disponibile in anteprima pubblica.
> Per partecipare all'anteprima, completare il sondaggio sull'onboarding di anteprima all'indirizzo [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
> Per accedere alla funzionalità di anteprima nella portale di Azure, è necessario usare l'URL seguente: [https://aka.ms/vmssadh](https://aka.ms/vmssadh) .
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="limitations"></a>Limitazioni

- Le dimensioni e i tipi di hardware disponibili per gli host dedicati variano in base all'area. Per ulteriori informazioni, fare riferimento alla [pagina dei prezzi](https://aka.ms/ADHPricing) dell'host.

## <a name="create-a-host-group"></a>Creare un gruppo host

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. È necessario creare un gruppo host in un'area e in una zona di disponibilità e aggiungervi gli host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. Con gli host dedicati è possibile usare una o entrambe le opzioni seguenti: 
- Estendersi tra più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ognuna delle zone che si desidera usare.
- Estendersi tra più domini di errore mappati a rack fisici. 
 
In entrambi i casi, è necessario specificare il numero di domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, usare un numero di domini di errore pari a 1. 

È anche possibile decidere di usare le zone di disponibilità e i domini di errore. 

In questo esempio verrà creato un gruppo host con 1 zona di disponibilità e 2 domini di errore. 


1. Aprire il [portale](https://portal.azure.com)di Azure. Se si vuole provare l'anteprima per la **selezione host automatica**, usare l'URL seguente: [https://aka.ms/vmssadh](https://aka.ms/vmssadh) .
1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra.
1. Cercare il **gruppo host** e quindi selezionare **gruppi host** dai risultati.
1. Nella pagina **gruppi host** selezionare **Crea**.
1. Selezionare la sottoscrizione che si vuole usare e quindi selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.
1. Digitare *myDedicatedHostsRG* come **nome** e quindi fare clic su **OK**.
1. Per **nome gruppo host**digitare *myHostGroup*.
1. In **Località** selezionare **Stati Uniti orientali**.
1. Per **zona di disponibilità**selezionare **1**.
1. Per **conteggio domini di errore**selezionare **2**.
1. Se è stato usato l'URL di **selezione host automatica** , selezionare questa opzione per assegnare automaticamente le VM e le istanze del set di scalabilità a un host disponibile in questo gruppo.
1. Selezionare **Verifica + crea** e quindi attendere la convalida.
1. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea** per creare il gruppo host.

La creazione del gruppo host dovrebbe richiedere solo alcuni istanti.


## <a name="create-a-dedicated-host"></a>Creazione di un host dedicato

A questo punto, creare un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU dell'host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.

Per altre informazioni sugli SKU e i prezzi degli host, vedere [Prezzi degli host dedicati di Azure](https://aka.ms/ADHPricing).

Se si imposta un numero di domini di errore per il gruppo host, verrà richiesto di specificare il dominio di errore per l'host.  

1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra.
1. Cercare **host dedicato** e quindi selezionare **host dedicati** dai risultati.
1. Nella pagina **host dedicati** selezionare **Crea**.
1. Selezionare la sottoscrizione che si vuole usare.
1. Selezionare *myDedicatedHostsRG* come **gruppo di risorse**.
1. In **Dettagli istanza**digitare *host* per **nome** e selezionare *Stati Uniti orientali* per la località.
1. In **profilo hardware**selezionare *standard ES3 Family-type 1* per la **famiglia di dimensioni**, selezionare *myHostGroup* per il **gruppo host** e quindi selezionare *1* per il **dominio di errore**. Lasciare le impostazioni predefinite per gli altri campi.
1. Al termine, selezionare **Verifica + crea** e attendere la convalida.
1. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea** per creare l'host.

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
1. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace cercare e selezionare l'immagine che si vuole usare, quindi scegliere **Crea**.
1. Nella scheda **nozioni di base** , in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta, quindi selezionare *myDedicatedHostsRG* come **gruppo di risorse**. 
1. Sotto **Dettagli dell'istanza**, digitare *myVM* come **nome della macchina virtuale** e scegliere *Stati Uniti orientali* come **Posizione**.
1. In **Opzioni di disponibilità** selezionare **zona di disponibilità**Selezionare *1* nell'elenco a discesa.
1. Per dimensioni selezionare **modifica dimensioni**. Nell'elenco delle dimensioni disponibili scegliere una dalla serie Esv3, ad esempio **E2S standard V3**. Potrebbe essere necessario cancellare il filtro per visualizzare tutte le dimensioni disponibili.
1. Completare il resto dei campi nella scheda **nozioni di base** , in base alle esigenze.
1. Nella parte superiore della pagina **selezionare la scheda** **Avanzate** e nella sezione **host** Selezionare *myHostGroup* per **gruppo host** *e host host.* 
    ![Seleziona gruppo host e host](./media/dedicated-hosts-portal/advanced.png)
1. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi e crea** nella parte inferiore della pagina.
1. Quando viene visualizzato il messaggio che la convalida è stata superata, selezionare **Crea**.

La distribuzione della macchina virtuale richiederà alcuni minuti.

## <a name="create-a-scale-set-preview"></a>Creare un set di scalabilità (anteprima)

> [!IMPORTANT]
> I set di scalabilità di macchine virtuali negli host dedicati sono attualmente in anteprima pubblica.
>
> Per partecipare all'anteprima, completare il sondaggio sull'onboarding di anteprima all'indirizzo [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
>
> Per accedere alla funzionalità di anteprima nella portale di Azure, è necessario usare l'URL seguente: [https://aka.ms/vmssadh](https://aka.ms/vmssadh) .
>
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
>
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando si distribuisce un set di scalabilità, è necessario specificare il gruppo host.

1. Cercare il *set di scalabilità* e selezionare i **set di scalabilità di macchine virtuali** nell'elenco.
1. Selezionare **Aggiungi** per creare un nuovo set di scalabilità.
1. Completare i campi nella scheda **nozioni di base** come in genere, ma assicurarsi di selezionare le dimensioni della macchina virtuale dalla serie selezionata per l'host dedicato, ad esempio **E2S standard V3**.
1. Nella scheda **Avanzate** per algoritmo di **distribuzione** Selezionare **distribuzione massima**.
1. In **gruppo host**selezionare il gruppo host dall'elenco a discesa. Se il gruppo è stato creato di recente, potrebbe essere necessario un minuto per aggiungerlo all'elenco.

## <a name="add-an-existing-vm"></a>Aggiungere una macchina virtuale esistente 

È possibile aggiungere una macchina virtuale di uscita a un host dedicato, ma la VM deve essere prima di tutto Stop\Deallocated. Prima di spostare una macchina virtuale in un host dedicato, verificare che la configurazione della macchina virtuale sia supportata:

- Le dimensioni della macchina virtuale devono essere nella stessa famiglia di dimensioni dell'host dedicato. Se, ad esempio, l'host dedicato è DSv3, è possibile che le dimensioni della macchina virtuale siano Standard_D4s_v3, ma non una Standard_A4_v2. 
- La macchina virtuale deve trovarsi nella stessa area dell'host dedicato.
- La macchina virtuale non può far parte di un gruppo di posizionamento di prossimità. Rimuovere la macchina virtuale dal gruppo di posizionamento vicino prima di trasferirla in un host dedicato. Per altre informazioni, vedere [spostare una macchina virtuale da un gruppo di posizionamento vicino](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- La macchina virtuale non può trovarsi in un set di disponibilità.
- Se la macchina virtuale si trova in una zona di disponibilità, deve corrispondere alla stessa zona di disponibilità del gruppo host. Le impostazioni della zona di disponibilità per la macchina virtuale e il gruppo host devono corrispondere.

Spostare la macchina virtuale in un host dedicato usando il [portale](https://portal.azure.com).

1. Aprire la pagina per la macchina virtuale.
1. Selezionare **Arresta** per stop\deallocate la macchina virtuale.
1. Scegliere **configurazione** dal menu a sinistra.
1. Selezionare un gruppo host e un host nei menu a discesa.
1. Al termine, selezionare **Salva** nella parte superiore della pagina.
1. Dopo che la macchina virtuale è stata aggiunta all'host, scegliere **Panoramica** dal menu a sinistra.
1. Nella parte superiore della pagina selezionare **Avvia** per riavviare la macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere la panoramica [Host dedicati](dedicated-hosts.md).

- È disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) un modello di esempio che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile distribuire un host dedicato usando l' [interfaccia](./linux/dedicated-hosts-cli.md) della riga di comando di Azure o [PowerShell](./windows/dedicated-hosts-powershell.md).
