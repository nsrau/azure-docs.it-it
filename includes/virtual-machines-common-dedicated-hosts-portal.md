---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 262880997c6b065dc5293a18d9a07c52ac836f37
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68702981"
---
> [!IMPORTANT]
> Gli host dedicati di Azure sono attualmente disponibili in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitazioni di anteprima note**
> - I set di scalabilità di macchine virtuali non sono attualmente supportati negli host dedicati.
> - La versione iniziale di anteprima supporta la serie di macchine virtuali seguente: DSv3 e ESv3. 


## <a name="create-a-host-group"></a>Creazione di un gruppo host

Un **gruppo host** è una nuova risorsa che rappresenta una raccolta di host dedicati. È possibile creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. Con gli host dedicati è possibile usare una o entrambe le opzioni seguenti: 
- Span tra più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ognuna delle zone che si desidera utilizzare.
- Si estende su più domini di errore di cui è stato eseguito il mapping ai rack fisici. 
 
In entrambi i casi, è necessario specificare il numero di domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, utilizzare un numero di domini di errore pari a 1. 

È anche possibile decidere di usare le zone di disponibilità e i domini di errore. 

In questo esempio verrà creato un gruppo host con 1 zona di disponibilità e 2 domini di errore. 


1. Aprire il [portale](https://portal.azure.com)di Azure.
1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra.
1. Cercare il **gruppo host** , quindi selezionare **gruppi host (anteprima)** dai risultati.

    ![Risultato della ricerca nei gruppi host.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. Nella pagina **gruppi host (anteprima)** selezionare **Crea**.
1. Selezionare la sottoscrizione che si vuole usare e quindi selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.
1. Digitare *myDedicatedHostsRG* come **nome** e quindi fare clic su **OK**.
1. Per **nome gruppo host**digitare *myHostGroup*.
1. Per **località**selezionare **Stati Uniti orientali**.
1. Per **zona di disponibilità**selezionare **1**.
1. Per **conteggio domini di errore**selezionare **2**.
1. Selezionare **Verifica + crea** e quindi attendere la convalida.

    ![Impostazioni gruppo host](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea** per creare il gruppo host.

La creazione del gruppo host dovrebbe richiedere solo alcuni istanti.

## <a name="create-a-dedicated-host"></a>Creazione di un host dedicato

A questo punto, creare un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.  Durante l'anteprima, sono supportati i valori SKU host seguenti: DSv3_Type1 e ESv3_Type1.

Per altre informazioni sugli SKU e i prezzi degli host, vedere [prezzi di host dedicati di Azure](https://aka.ms/ADHPricing).

Se si imposta un numero di domini di errore per il gruppo host, verrà richiesto di specificare il dominio di errore per l'host.  

1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra.
1. Cercare **host dedicato** , quindi selezionare **host dedicati (anteprima)** dai risultati.

    ![Risultato della ricerca nei gruppi host.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. Nella pagina **host dedicati (anteprima)** selezionare **Crea**.
1. Selezionare la sottoscrizione che si vuole usare.
1. Selezionare *myDedicatedHostsRG* come **gruppo di risorse**.
1. In **Dettagli istanza**digitare *host* per **nome** e selezionare *Stati Uniti orientali* per la località.
1. In **profilo hardware**selezionare *standard ES3 Family-type 1* per la **famiglia di dimensioni**, selezionare *myHostGrup* per il **gruppo host** e quindi selezionare *1* per il **dominio di errore**. Lasciare le impostazioni predefinite per gli altri campi.
1. Al termine, selezionare **Verifica + crea** e attendere la convalida.

    ![Impostazioni host](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea** per creare l'host.


