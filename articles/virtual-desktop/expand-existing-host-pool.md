---
title: Espandere il pool host esistente con nuovi host di sessione - AzureExpand existing host with new session hosts - Azure
description: Come espandere un pool host esistente con nuovi host di sessione in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365220"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Espandere un pool host esistente con nuovi host di sessione

Man mano che si aumenta l'utilizzo all'interno del pool host, potrebbe essere necessario espandere il pool host esistente con nuovi host di sessione per gestire il nuovo carico.

Questo articolo illustra come espandere un pool host esistente con nuovi host di sessione.

## <a name="what-you-need-to-expand-the-host-pool"></a>Elementi necessari per espandere il pool host

Prima di iniziare, assicurarsi di aver creato un pool host e macchine virtuali host sessione (VM) usando uno dei metodi seguenti:Before you start, make sure you've created a host pool and session host virtual machines (VMs) using one of the following methods:

- [Offerta di Azure Marketplace](./create-host-pools-azure-marketplace.md)
- [Modello GitHub Azure Resource Manager](./create-host-pools-arm-template.md)
- [Creare un pool di host con PowerShell](./create-host-pools-powershell.md)

Saranno inoltre necessarie le informazioni seguenti da quando sono state create per la prima volta le macchine virtuali host del pool host e della sessione:You'll also need the following information from when you first created the host pool and session host VMs:

- Dimensione della macchina virtuale, immagine e prefisso del nomeVM size, image, and name prefix
- Aggiunta al dominio e credenziali di amministratore tenant di Windows Virtual Desktop
- Nome della rete virtuale e della subnet

Le tre sezioni successive sono tre metodi che è possibile utilizzare per espandere il pool host. È possibile eseguire una delle due opzioni con qualsiasi strumento di distribuzione con cui si ha familiarità.

>[!NOTE]
>Durante la fase di distribuzione, verranno visualizzati messaggi di errore per le risorse della macchina virtuale dell'host di sessione precedente se sono attualmente arrestate. Questi errori si verificano perché Azure non può eseguire l'estensione DSC di PowerShell per verificare che le macchine virtuali host della sessione siano registrate correttamente nel pool host esistente. È possibile ignorare questi errori oppure evitare gli errori avviando tutte le macchine virtuali host della sessione nel pool host esistente prima di avviare il processo di distribuzione.

## <a name="redeploy-from-azure"></a>Ridistribuire da AzureRedeploy from Azure

Se sono già state create macchine virtuali host di pool host e sessione usando l'offerta di Azure Marketplace o il [modello GitHub Azure Resource Manager,](./create-host-pools-arm-template.md)è possibile ridistribuire lo stesso modello dal portale di Azure.If you've already created a host pool and session host VMs using the [Azure Marketplace offering](./create-host-pools-azure-marketplace.md) or GitHub Azure Resource Manager template , you can redeploy the same template from the Azure portal. La ridistribuzione del modello rientra automaticamente tutte le informazioni immesse nel modello originale, ad eccezione delle password.

Ecco come ridistribuire il modello di Azure Resource Manager per espandere un pool host:Here's how to redeploy the Azure Resource Manager template to expand a host pool:

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Nella barra di ricerca nella parte superiore del portale di Azure cercare **Gruppi di** risorse e selezionare l'elemento in **Servizi.**
3. Trovare e selezionare il gruppo di risorse creato al momento della creazione del pool host.
4. Nel riquadro sul lato sinistro del browser selezionare **Distribuzioni**.
5. Selezionare la distribuzione appropriata per il processo di creazione del pool host:
     - Se è stato creato il pool host originale con l'offerta Azure Marketplace, selezionare la distribuzione a partire da **rds.wvd-provision-host-pool**.
     - Se il pool host originale è stato creato con il modello GitHub Azure Resource Manager, selezionare la distribuzione denominata **Microsoft.Template**.
6. Selezionare **Ridistribuisci**.
     
     >[!NOTE]
     >Se il modello non viene ridistribuito automaticamente quando si seleziona **Ridistribui**, selezionare **Modello** nel riquadro sul lato sinistro del browser, quindi selezionare **Distribuisci**.

7. Selezionare il gruppo di risorse che contiene le macchine virtuali host della sessione corrente nel pool host esistente.
     
     >[!NOTE]
     >Se viene visualizzato un errore che indica di selezionare un gruppo di risorse diverso anche se quello immesso è corretto, selezionare un altro gruppo di risorse, quindi selezionare il gruppo di risorse originale.

8. Immettere l'URL seguente per *l'_artifactsLocation:*`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Immettere il nuovo numero totale di host di sessione desiderati in *Numero di istanze Rdsh*. Ad esempio, se si espande il pool host da cinque host di sessione a otto, immettere **8**.
10. Immettere la stessa password di dominio esistente utilizzata per l'UPN di dominio esistente. Non modificare il nome utente, perché ciò causerà un errore quando si esegue il modello.
11. Immettere la stessa password di amministratore tenant utilizzata per l'ID utente o applicazione immesso per *Upn amministratore tenant o ID applicazione*. Ancora una volta, non modificare il nome utente.
12. Completare l'invio per espandere il pool host.

## <a name="run-the-azure-marketplace-offering"></a>Eseguire l'offerta di Azure MarketplaceRun the Azure Marketplace offering

Seguire le istruzioni in [Creare un pool host usando Azure Marketplace](./create-host-pools-azure-marketplace.md) fino a raggiungere Esegui [l'offerta](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)di Azure Marketplace per il provisioning di un nuovo pool host. Quando si arriva a quel punto, è necessario immettere le seguenti informazioni per ogni scheda:

### <a name="basics"></a>Nozioni di base

Tutti i valori in questa sezione devono corrispondere a quelli forniti quando sono state create per la prima volta il pool host e le macchine virtuali host della sessione, ad eccezione di *Utenti desktop predefiniti:*

1.    In *Sottoscrizione*selezionare la sottoscrizione in cui è stato creato per la prima volta il pool host.
2.    Per *Gruppo di*risorse selezionare lo stesso gruppo di risorse in cui si trovano le macchine virtuali dell'host di sessione del pool host esistente.
3.    Per *Area*, selezionare la stessa area in cui si trovano le macchine virtuali host sessione pool host esistenti.
4.    In *Nome hostpool*immettere il nome del pool host esistente.
5.    Per *Tipo di*desktop , selezionare il tipo di desktop corrispondente al pool host esistente.
6.    Per *Utenti desktop predefiniti,* immettere un elenco separato da virgole di tutti gli utenti aggiuntivi che si desidera accedere ai client Desktop virtuale di Windows e accedere a un desktop al termine dell'offerta di Azure Marketplace. Ad esempio, se si user3@contoso.com user4@contoso.com desidera assegnare e accedere, immettere user3@contoso.com,user4@contoso.com.
7.    Selezionare **Avanti : Configura macchina virtuale**.

>[!NOTE]
>Ad eccezione di *Utenti desktop predefiniti,* tutti i campi devono corrispondere esattamente a ciò che è stato configurato nel pool host esistente. Se si verifica una mancata corrispondenza che comporterà un nuovo pool host.

### <a name="configure-virtual-machines"></a>Configurare le macchine virtuali

Tutti i valori dei parametri in questa sezione devono corrispondere a quelli forniti quando si crea il pool host e le macchine virtuali dell'host di sessione, ad eccezione del numero totale di macchine virtuali. Il numero di macchine virtuali immesse sarà il numero di macchine virtuali nel pool host espanso:The number of VMs you enter will the number of VMs in your expanded host pool:

1. Selezionare la dimensione della macchina virtuale che corrisponde alle macchine virtuali host della sessione esistente.
    
    >[!NOTE]
    >Se le dimensioni specifiche della VM che si cercano non vengono visualizzate nel selettore di dimensioni, significa che non ne è stato ancora eseguito l'onboarding nello strumento di Azure Marketplace. Per richiedere le dimensioni della VM, creare una richiesta oppure aggiornarne una esistente nel [forum UserVoice di Desktop virtuale Windows](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Personalizzare i parametri *Profilo di utilizzo*, Utenti *totali*e Numero di *macchine virtuali* per selezionare il numero totale di host di sessione che si desidera avere nel pool host. Ad esempio, se si espande il pool host da cinque host di sessione a otto, configurare queste opzioni per arrivare a 8 macchine virtuali.
3. Immettere un prefisso per i nomi delle macchine virtuali. Ad esempio, se si immette il nome "prefisso", le macchine virtuali saranno denominate "prefisso-0", "prefisso-1" e così via.
4. Selezionare **Avanti : Impostazioni macchina virtuale**.

### <a name="virtual-machine-settings"></a>Impostazioni della macchina virtuale

Tutti i valori dei parametri in questa sezione devono corrispondere a quelli forniti quando sono state create per la prima volta le macchine virtuali host del pool host e della sessione:All parameter values in this section should match what you provided when you first created the host pool and session host VMs:

1. Per *Origine immagine* e Versione del sistema operativo *immagine*, immettere le stesse informazioni fornite al momento della creazione del pool host.
2. Per l'aggiunta a *UN UPN* e le password associate al dominio di Active Directory, immettere le stesse informazioni fornite quando è stato creato per la prima volta il pool host per aggiungere le macchine virtuali al dominio di Active Directory. Queste credenziali verranno utilizzate per creare un account locale nelle macchine virtuali. È possibile reimpostare questi account locali per modificare le credenziali in un secondo momento.
3. Per le informazioni sulla rete virtuale, selezionare la stessa rete virtuale e la stessa subnet in cui si trovano le macchine virtuali host del pool host esistenti.
4. Selezionare **Avanti : Configurare le informazioni**di Windows Virtual Desktop .

### <a name="windows-virtual-desktop-information"></a>Informazioni sul desktop virtuale di Windows

Tutti i valori dei parametri in questa sezione devono corrispondere a quelli forniti quando sono state create per la prima volta le macchine virtuali host del pool host e della sessione:All parameter values in this section should match what you provided when you first created the host pool and session host VMs:

1. Per *Windows Virtual Desktop tenant group name* (Nome gruppo di tenant Desktop virtuale Windows), immettere il nome del gruppo di tenant che contiene il tenant. Lasciare il valore predefinito se non è stato fornito un nome specifico per il gruppo di tenant.
2. Per *Windows Virtual Desktop tenant name* (Nome tenant Desktop virtuale Windows) immettere il nome del tenant in cui verrà creato il pool di host.
3. Specificare le stesse credenziali usate quando sono state create per la prima volta le macchine virtuali del pool host e dell'host di sessione. Se si usa un'entità servizio, immettere l'ID dell'istanza di Azure Active Directory in cui si trova l'entità servizio.
4. Selezionare **Avanti : Rivedere e creare**.

## <a name="run-the-github-azure-resource-manager-template"></a>Eseguire il modello GitHub Azure Resource ManagerRun the GitHub Azure Resource Manager template

Seguire le istruzioni in Eseguire il modello di [Azure Resource Manager per il provisioning](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) di un nuovo pool host e fornire tutti gli stessi valori di parametro, ad eccezione del numero di *istanze Rdsh*. Immettere il numero di macchine virtuali host della sessione desiderato nel pool host dopo l'esecuzione del modello. Ad esempio, se si espande il pool host da cinque host di sessione a otto, immettere **8**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver ampliato il pool host esistente, è possibile accedere a un client Windows Virtual Desktop per testarlo come parte di una sessione utente. È possibile connettersi a una sessione con uno dei seguenti client:

- [Connettersi con il client desktop di Windows](./connect-windows-7-and-10.md)
- [Connettersi con il client Web](./connect-web.md)
- [Connettersi con il client Android](./connect-android.md)
- [Connettersi con il client macOS](./connect-macos.md)
- [Connettersi con il client iOS](./connect-ios.md)
