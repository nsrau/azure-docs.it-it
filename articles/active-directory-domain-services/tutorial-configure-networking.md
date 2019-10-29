---
title: Esercitazione - Configurare una rete virtuale per Azure AD Domain Services | Microsoft Docs
description: Questa esercitazione illustra come creare e configurare una subnet di rete virtuale o un peering di reti per un'istanza di Azure Active Directory Domain Services tramite il portale di Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: iainfou
ms.openlocfilehash: 8f4251c1cda27bab3e67e5272a0ef1a94595dac3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757697"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Esercitazione: Configurare una rete virtuale per un'istanza di Azure Active Directory Domain Services

Per fornire connettività a utenti e applicazioni, un dominio gestito di Azure Active Directory Domain Services (Azure AD DS) viene distribuito in una subnet di rete virtuale di Azure. Questa subnet di rete virtuale dovrà essere usata solo per le risorse del dominio gestito fornite dalla piattaforma Azure. Le VM e le applicazioni personalizzate che si creano non devono essere distribuite nella stessa subnet di rete virtuale. È invece necessario creare e distribuire le applicazioni in una subnet di rete virtuale distinta oppure in una rete virtuale distinta collegata in peering alla rete virtuale di Azure AD DS.

Questa esercitazione illustra come creare e configurare una subnet di rete virtuale dedicata oppure come collegare in peering una rete diversa alla rete virtuale di un dominio gestito di Azure AD DS.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Identificare le opzioni di connettività di reti virtuali per le risorse aggiunte a dominio in Azure AD DS
> * Creare un intervallo di indirizzi IP e una subnet aggiuntiva nella rete virtuale di Azure AD DS
> * Configurare il peering della rete virtuale a una rete separata da Azure AD DS

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Per abilitare Azure AD DS, sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* Per creare le risorse di Azure AD DS richieste, sono necessari privilegi di *collaboratore* nella sottoscrizione di Azure.
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione viene creata e configurata l'istanza di Azure AD DS tramite il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Opzioni di connettività dei carichi di lavoro delle applicazioni

Nell'esercitazione precedente è stato creato un dominio gestito di Azure AD DS che usa alcune opzioni di configurazione predefinite per la rete virtuale. Con queste opzioni predefinite sono state create una rete virtuale e una subnet di rete virtuale di Azure. A questa subnet di rete virtuale sono connessi i controller di dominio di Azure AD DS che forniscono i servizi di dominio gestito.

Quando si creano ed eseguono VM che devono usare il dominio gestito di Azure AD DS, è necessario fornire connettività di rete. Questa connettività di rete può essere fornita in uno dei modi seguenti:

* Creare una subnet di rete virtuale aggiuntiva nella rete virtuale predefinita del dominio gestito di Azure AD DS. Questa subnet aggiuntiva è quella in cui si creano e si connettono le VM.
    * Poiché le VM fanno parte della stessa rete virtuale, possono eseguire automaticamente la risoluzione dei nomi e comunicare con i controller di dominio di Azure AD DS.
* Configurare il peering di reti virtuali di Azure dalla rete virtuale del dominio gestito di Azure AD DS a una o più reti virtuali separate. Queste reti virtuali separate sono quelle in cui si creano e si connettono le VM.
    * Quando si configura il peering di reti virtuali, è necessario configurare anche le impostazioni DNS per usare la risoluzione dei nomi per i controller di dominio di Azure AD DS.

In genere si usa solo una di queste opzioni di connettività di rete. La scelta dipende solitamente da come si vogliono gestire separatamente le risorse di Azure. Se si vogliono gestire Azure AD DS e le VM connesse come un unico gruppo di risorse, è possibile creare una subnet di rete virtuale aggiuntiva per le macchine virtuali. Se si vuole separare la gestione di Azure AD DS e delle VM connesse, è possibile usare il peering di reti virtuali. È anche possibile scegliere di usare il peering di reti virtuali per fornire connettività alle VM esistenti nell'ambiente di Azure che sono connesse a una rete virtuale esistente.

In questa esercitazione è sufficiente configurare una di queste opzioni di connettività di reti virtuali.

Per altre informazioni su come pianificare e configurare la rete virtuale, vedere le [considerazioni sulla rete per Azure Active Directory Domain Services][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Creare una subnet di rete virtuale

Per impostazione predefinita, la rete virtuale di Azure creata con il dominio gestito di Azure AD DS contiene una singola subnet di rete virtuale. Questa subnet di rete virtuale dovrà essere usata solo dalla piattaforma Azure per fornire servizi di dominio gestito. Per creare e usare le proprie VM in questa rete virtuale di Azure, creare una subnet aggiuntiva.

Per creare una subnet di rete virtuale per le VM e i carichi di lavoro delle applicazioni, completare i passaggi seguenti:

1. Nel portale di Azure selezionare il gruppo di risorse del dominio gestito di Azure AD DS, ad esempio *myResourceGroup*. Nell'elenco di risorse scegliere la rete virtuale predefinita, ad esempio *aadds-vnet*.
1. Nel menu sinistro della finestra della rete virtuale selezionare **Spazio degli indirizzi**. La rete virtuale viene creata con un singolo spazio di indirizzi, *10.0.1.0/24*, usato dalla subnet predefinita.

    Aggiungere un intervallo di indirizzi IP aggiuntivo alla rete virtuale. Le dimensioni di questo intervallo di indirizzi e l'intervallo di indirizzi IP effettivo da usare dipendono dalle altre risorse di rete già distribuite. L'intervallo di indirizzi IP non deve sovrapporsi agli intervalli di indirizzi esistenti nell'ambiente di Azure o in locale. Assicurarsi di scegliere dimensioni dell'intervallo di indirizzi IP sufficienti per il numero di VM che si prevede di distribuire nella subnet.

    Nell'esempio seguente viene aggiunto l'intervallo di indirizzi IP *10.0.2.0/24*. Al termine, selezionare **Salva**.

    ![Aggiungere un altro intervallo di indirizzi IP di rete virtuale nel portale di Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Successivamente, nel menu sinistro della finestra della rete virtuale selezionare **Subnet**, quindi scegliere **+ Subnet** per aggiungere una subnet.
1. Immettere un nome per la subnet, ad esempio *workloads*. Se necessario, aggiornare l'opzione **Intervallo di indirizzi**  se si vuole usare un sottoinsieme dell'intervallo di indirizzi IP configurato per la rete virtuale nei passaggi precedenti. Per il momento, lasciare le impostazioni predefinite per opzioni come gruppo di sicurezza di rete, tabella di route ed endpoint di servizio.

    Nell'esempio seguente viene creata una subnet denominata *workloads* che usa l'intervallo di indirizzi IP *10.0.2.0/24*:

    ![Aggiungere un'altra subnet di rete virtuale nel portale di Azure](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Al termine, selezionare **OK**. La creazione della subnet di rete virtuale richiede alcuni secondi.

Quando si crea una macchina virtuale che deve usare il dominio gestito di Azure AD DS, assicurarsi di selezionare questa subnet di rete virtuale. Non creare VM nella subnet *aadds-subnet* predefinita. Se si seleziona una rete virtuale diversa, la connettività di rete e la risoluzione DNS non sono disponibili per raggiungere il dominio gestito di Azure AD DS, a meno che non si configuri il peering di reti virtuali.

## <a name="configure-virtual-network-peering"></a>Configurare il peering reti virtuali

È possibile che sia già presente una rete virtuale di Azure per le macchine virtuali o che si voglia lasciare separata la rete virtuale del dominio gestito di Azure AD DS. Per usare il dominio gestito, le VM di altre reti virtuali devono poter comunicare con i controller di dominio di Azure AD DS. Questa connettività può essere fornita tramite il peering di reti virtuali di Azure.

Con il peering di reti virtuali di Azure, vengono connesse due reti virtuali tra loro, senza la necessità di un dispositivo VPN (Virtual Private Network). Il peering di reti consente di connettere rapidamente le reti virtuali e di definire i flussi del traffico nell'ambiente di Azure. Per altre informazioni sul peering, vedere la [panoramica sul peering di reti virtuali di Azure][peering-overview].

Per collegare in peering una rete virtuale alla rete virtuale del dominio gestito di Azure AD DS, completare i passaggi seguenti:

1. Scegliere la rete virtuale predefinita creata per l'istanza di Azure AD DS denominata *aadds-vnet*.
1. Nel menu sinistro della finestra della rete virtuale selezionare **Peering**.
1. Per creare un peering, selezionare **+ Aggiungi**. Nell'esempio seguente, la rete virtuale predefinita *aadds-vnet* viene collegata in peering a una rete virtuale denominata *myVnet*. Configurare le impostazioni seguenti con i propri valori:

    * **Nome del peering da aadds-vnet a rete virtuale remota**: un identificatore descrittivo delle due reti, ad esempio *aadds-vnet-to-myvnet*
    * **Modello di distribuzione della rete virtuale**: *Gestione risorse*
    * **Sottoscrizione** la sottoscrizione della rete virtuale per cui creare il peering, ad esempio *Azure*.
    * **Rete virtuale**: la rete virtuale per cui creare il peering, ad esempio *myVnet*.
    * **Nome del peering da myVnet a aadds-vnet**: un identificatore descrittivo delle due reti, ad esempio *myvnet-to-aadds-vnet*

    ![Configurare il peering di reti virtuali nel portale di Azure](./media/tutorial-configure-networking/create-peering.png)

    Lasciare le altre impostazioni predefinite per l'accesso alla rete virtuale o il traffico inoltrato a meno che non si abbiano requisiti specifici per l'ambiente, quindi selezionare **OK**.

1. La creazione del peering nella rete virtuale di Azure AD DS e in quella selezionata richiede alcuni secondi. Al termine, per **Stato peering** è indicato *Connesso*, come illustrato nell'esempio seguente:

    ![Reti connesse correttamente in peering nel portale di Azure](./media/tutorial-configure-networking/connected-peering.png)

Prima che le VM nella rete virtuale con peering possano usare il dominio gestito di Azure AD DS, è necessario configurare i server DNS in modo da consentire la risoluzione dei nomi corretta.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Configurare i server DNS nella rete virtuale con peering

Per consentire alle VM e alle applicazioni nella rete virtuale con peering di comunicare correttamente con il dominio gestito di Azure AD DS, è necessario aggiornare le impostazioni DNS. Gli indirizzi IP dei controller di dominio di Azure AD DS devono essere configurati come server DNS nella rete virtuale con peering. È possibile configurarli in due modi:

* Configurare i server DNS della rete virtuale di Azure per l'uso dei controller di dominio di Azure AD DS.
* Configurare il server DNS esistente in uso nella rete virtuale con peering per l'uso dell'inoltro DNS condizionale in modo da indirizzare le query al dominio gestito di Azure AD DS. Questa procedura varia a seconda del server DNS esistente in uso.

Per questa esercitazione, configurare i server DNS della rete virtuale di Azure in modo da indirizzare tutte le query ai controller di dominio di Azure AD DS.

1. Nel portale di Azure selezionare il gruppo di risorse della rete virtuale con peering, ad esempio *myResourceGroup*. Nell'elenco di risorse scegliere la rete virtuale con peering, ad esempio *myVnet*.
1. Nel menu sinistro della finestra della rete virtuale selezionare **Server DNS**.
1. Per impostazione predefinita, una rete virtuale usa i server DNS predefiniti forniti da Azure. Scegliere di usare server DNS **personalizzati**. Immettere gli indirizzi IP per i controller di dominio di Azure AD DS, che in genere sono *10.0.1.4* e *10.0.1.5*. Confermare questi indirizzi IP nella finestra **Panoramica** del dominio gestito di Azure AD DS nel portale.

    ![Configurare i server DNS della rete virtuale per l'uso dei controller di dominio di Azure AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Al termine, selezionare **Salva**. L'aggiornamento dei server DNS per la rete virtuale richiede alcuni secondi.
1. Per applicare le impostazioni DNS aggiornate alle macchine virtuali, riavviare le VM connesse alla rete virtuale con peering.

Quando si crea una VM che deve usare il dominio gestito di Azure AD DS, assicurarsi di selezionare questa rete virtuale con peering. Se si seleziona una rete virtuale diversa, la connettività di rete e la risoluzione DNS non sono disponibili per raggiungere il dominio gestito di Azure AD DS.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Identificare le opzioni di connettività di reti virtuali per le risorse aggiunte a dominio in Azure AD DS
> * Creare un intervallo di indirizzi IP e una subnet aggiuntiva nella rete virtuale di Azure AD DS
> * Configurare il peering della rete virtuale a una rete separata da Azure AD DS

Per vedere il dominio gestito in azione, creare e aggiungere una macchina virtuale al dominio.

> [!div class="nextstepaction"]
> [Aggiungere una macchina virtuale Windows Server a un dominio gestito](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
