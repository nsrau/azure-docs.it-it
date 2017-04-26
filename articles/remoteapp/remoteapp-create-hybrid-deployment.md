---
title: Come creare una raccolta ibrida per Azure RemoteApp | Microsoft Docs
description: Informazioni su come creare una distribuzione di RemoteApp si connette alla rete interna.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 08ea0ce3-3a2c-4ddf-9394-6d75c8030cb1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: ae8c26069503a24f3ba17a86160801c60a68d668
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Come creare una raccolta ibrida per Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Sono disponibili due tipi di raccolte Azure RemoteApp:

* Cloud: risiede completamente in Azure. È possibile scegliere di salvare tutti i dati nel cloud (raccolta solo cloud) o connettere la raccolta a una rete virtuale e salvare i dati in questa posizione.   
* Ibrida: include una rete virtuale per l'accesso in locale, è necessario l'uso di Azure Active Directory e un ambiente Active Directory locale.

Non si sa cosa è necessario? Vedere [Tipo di raccolta necessario per RemoteApp di Azure](remoteapp-collections.md).

Questa esercitazione illustra la creazione di una raccolta ibrida. Sono previsti otto passaggi:

1. Decidere quale [immagine](remoteapp-imageoptions.md) utilizzare per la raccolta. È possibile creare un'immagine personalizzata oppure usare una delle immagini Microsoft incluse nella sottoscrizione.
2. Configurare la rete virtuale. Vedere le informazioni sulla [pianificazione di reti virtuali](remoteapp-planvnet.md) e sulle [dimensioni](remoteapp-vnetsizing.md) di queste ultime.
3. Creare una raccolta.
4. Aggiungere la raccolta al dominio locale.
5. Aggiungere un'immagine modello alla raccolta.
6. Configurare la sincronizzazione della directory. RemoteApp di Azure richiede l'integrazione con Azure Active Directory 1) configurando Azure Active Directory Sync con l'opzione Sincronizzazione password o 2) configurando Azure Active Directory Sync senza l'opzione Sincronizzazione password ma usando un dominio federato ad ADFS. Verificare le [informazioni di configurazione per Active Directory con RemoteApp](remoteapp-ad.md).
7. Pubblicare app di RemoteApp.
8. Configurare l'accesso utente.

**Prima di iniziare**

Prima di creare la raccolta, è necessario eseguire le operazioni seguenti:

* [Accedere](https://azure.microsoft.com/services/remoteapp/) ad Azure RemoteApp.
* Creare in Active Directory un account utente da usare come account del servizio RemoteApp di Azure. Limitare le autorizzazioni per l'account in modo che possa aggiungere computer al dominio.
* Raccogliere informazioni sulla rete locale: informazioni sull'indirizzo IP e dettagli sul dispositivo VPN.
* Installare il modulo [Azure PowerShell](/powershell/azureps-cmdlets-docs) .
* Raccogliere informazioni sugli utenti a cui concedere l'accesso. È necessario il nome dell'entità utente Azure Active Directory (ad esempio, name@contoso.com) per ogni utente. Assicurarsi che il nome UPN corrisponda tra Azure Active Directory e Active Directory.
* Scegliere un'immagine modello. Un'immagine modello di RemoteApp di Azure contiene le app e i programmi da pubblicare per gli utenti. Per altre informazioni, vedere [Opzioni immagine di RemoteApp di Azure](remoteapp-imageoptions.md) .
* Se si desidera usare l'immagine di Office 365 ProPlus, consultare le informazioni in [questo articolo](remoteapp-officesubscription.md).
* [Configurare Active Directory per RemoteApp](remoteapp-ad.md).

## <a name="step-1-set-up-your-virtual-network"></a>Passaggio 1: configurare la rete virtuale
È possibile distribuire una raccolta ibrida che utilizza una rete virtuale di Azure esistente oppure è possibile creare una nuova rete virtuale. Una rete virtuale consente agli utenti di accedere ai dati nella rete locale mediante le risorse remote di RemoteApp. L'uso di una rete virtuale di Azure offre alla raccolta accesso di rete diretto ad altri servizi e macchine virtuali di Azure distribuiti in tale rete virtuale.

Assicurarsi di rivedere le informazioni relative alla [panificazione della rete virtuale](remoteapp-planvnet.md) e alle [dimensioni delle reti virtuali](remoteapp-vnetsizing.md) prima di creare una rete virtuale.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Creare una rete virtuale di Azure e aggiungerla alla distribuzione di Active Directory
Per iniziare, creare una [rete virtuale](../virtual-network/virtual-networks-create-vnet-arm-pportal.md), selezionando la scheda **Rete** nel portale di Azure. È necessario connettere la rete virtuale alla distribuzione di Active Directory sincronizzata con il tenant di Azure Active Directory.

Per altre informazioni, vedere [Creare una rete virtuale usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Assicurarsi che la rete virtuale sia pronta per RemoteApp di Azure
Prima di creare la raccolta, verificare che la nuova rete virtuale sia pronta. A questo scopo, eseguire le seguenti operazioni:

1. Creare una macchina virtuale di Azure all'interno della subnet della rete virtuale appena creata per RemoteApp.
2. Connettersi alla macchina virtuale tramite Desktop remoto (facendo clic su **Connetti**).
3. Collegarla alla stessa distribuzione di Active Directory che si desidera usare per RemoteApp.

Ha funzionato? La rete e la subnet virtuali sono pronte per RemoteApp di Azure!

Altre informazioni sulla creazione di macchine virtuali di Azure e su come collegarle a Desktop remoto sono disponibili [qui](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Passaggio 2: Creare una raccolta di RemoteApp di Azure.
1. Nel [Portale di Azure](http://manage.windowsazure.com), passare alla pagina RemoteApp di Azure.
2. Fare clic su **Nuovo > Crea con rete virtuale**.
3. Immettere un nome per la raccolta.
4. Scegliere il piano che da usare: standard o di base.
5. Scegliere la rete virtuale nell'elenco a discesa, poi scegliere la subnet.
6. Scegliere di aggiungerla al dominio.
7. Fare clic su **Crea raccolta RemoteApp**.

Dopo avere creato la raccolta di Azure RemoteApp, fare doppio clic sul nome della raccolta. Questo visualizzerà la pagina **Avvio rapido** - qui è possibile completare la configurazione della raccolta.

Nel caso in cui si siano verificati problemi Consultare le [informazioni sulla risoluzione dei problemi della raccolta ibrida](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Passaggio 3: Collegare la raccolta al dominio locale
1. Nella pagina **Avvio rapido** fare clic su **join a local domain** (Aggiungi un dominio locale).
2. Aggiungere l'account del servizio RemoteApp di Azure al dominio di Active Directory locale. È necessario disporre di nome di dominio, unità organizzativa, nome utente e password dell'account del servizio.
   
    Si tratta delle informazioni raccolte se è stata seguita la procedura descritta in [Configurare Active Directory per Azure RemoteApp](remoteapp-ad.md).

## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Passaggio 4: Creare un collegamento ad un'immagine di Azure RemoteApp
Un'immagine modello di RemoteApp di Azure contiene i programmi da condividere con gli utenti. È possibile creare una nuova [immagine modello](remoteapp-imageoptions.md) o creare un collegamento a un'immagine esistente (già importata o caricata in Azure RemoteApp). È anche possibile creare un collegamento a [immagini modello](remoteapp-images.md) di RemoteApp di Azure che contengono applicazioni Office 365 o Office 2013 (per l'uso in valutazione).

Se si carica la nuova immagine, è necessario immettere il nome e scegliere il percorso dell'immagine. Nella pagina successiva della procedura guidata verrà visualizzato un insieme di cmdlet di PowerShell. Copiarli ed eseguirli da un prompt di Windows PowerShell con privilegi elevati per caricare l'immagine specificata.

Se si crea un collegamento a un'immagine modello esistente, è sufficiente specificare il nome, il percorso e la sottoscrizione di Azure associata all'immagine.

## <a name="step-5-configure-active-directory-directory-synchronization"></a>Passaggio 5: Configurare la sincronizzazione della directory di Active Directory
RemoteApp di Azure richiede l'integrazione con Azure Active Directory 1) configurando Azure Active Directory Sync con l'opzione Sincronizzazione password o 2) configurando Azure Active Directory Sync senza l'opzione Sincronizzazione password ma usando un dominio federato ad ADFS.

Consultare [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) - questo articolo consente di impostare l'integrazione di directory in 4 passaggi.

Per informazioni sulla pianificazione e le procedure dettagliate, vedere [Roadmap sulla sincronizzazione della directory](http://msdn.microsoft.com//library/azure/hh967642.aspx) .

## <a name="step-6-publish-apps"></a>Passaggio 6: Pubblicare le app
Un'app di Azure RemoteApp è l'app o il programma da fornire ai propri utenti. Si trova nell'immagine modello caricata per la raccolta. Quando un utente accede a un'app, questa si comporta come se venisse eseguita nel suo ambiente locale, mentre in realtà viene eseguita in Azure.

Prima che gli utenti possano accedere alle app, è necessario pubblicarle. In questo modo, gli utenti possono accedere tramite il client di Desktop remoto.

È possibile pubblicare più app nella raccolta. Nella pagina di pubblicazione fare clic su **Pubblica** per aggiungere un’app. È possibile eseguire la pubblicazione dal menu **Start** dell'immagine modello oppure specificando il percorso nell'immagine modello dell'app. Se si sceglie di aggiungere il programma dal menu **Start** , scegliere il programma da aggiungere. Se si sceglie di fornire il percorso all'app, specificare il nome dell'app e il percorso in cui è installata nell'immagine modello.

## <a name="step-7-configure-user-access"></a>Passaggio 7: Configurare l'accesso utente
A questo punto, dopo avere creato la raccolta, è necessario aggiungere gli utenti che potranno usare le risorse remote. Gli utenti o i gruppi a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare la raccolta di Azure RemoteApp.

1. Nella pagina Avvio rapido fare clic su **Configura accesso utente**.
2. Immettere l'account di lavoro (da Active Directory) o l'account Microsoft al quale concedere l'accesso.
   
   **Note:**
   
   Assicurarsi di usare il formato *user@domain.com*.
   
   Se si usa Office 365 ProPlus alla raccolta, è necessario usare le identità di Active Directory per gli utenti. Ciò consente di convalidare la licenza.
3. Dopo la convalida degli utenti, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
La procedura è stata completata e la raccolta ibrida RemoteApp di Azure è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di Azure RemoteApp. Chiedere quindi agli utenti di connettersi al client e di accedere alle app pubblicate.

### <a name="help-us-help-you"></a>Come contribuire al miglioramento
Non tutti sanno che oltre alla classificazione di questo articolo e all'aggiunta di commenti di seguito, è possibile apportare modifiche all'articolo stesso. Mancano informazioni? Alcune informazioni non sono corrette? Qualcosa non è abbastanza chiaro? Scorrere verso l'alto e fare clic su **Modifica in GitHub** per apportare modifiche e suggerire miglioramenti, che saranno esaminati e approvati per poi essere applicati a questo articolo.


