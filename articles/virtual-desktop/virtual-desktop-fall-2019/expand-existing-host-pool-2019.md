---
title: Espandi pool host esistente con nuovi host sessione-Azure
description: Come espandere un pool di host esistente con nuovi host di sessione nel desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f4313f36a0b250be9646c6658b98f15037374729
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615526"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Espandi un pool di host esistente con nuovi host sessione

>[!IMPORTANT]
>Questo contenuto si applica alla versione 2019, che non supporta Azure Resource Manager oggetti desktop virtuali di Windows. Se si sta tentando di gestire Azure Resource Manager oggetti desktop virtuali Windows introdotti nell'aggiornamento di Spring 2020, vedere [questo articolo](../expand-existing-host-pool.md).

Quando si aumenta l'utilizzo all'interno del pool host, potrebbe essere necessario espandere il pool host esistente con nuovi host di sessione per gestire il nuovo carico.

In questo articolo viene illustrato come espandere un pool di host esistente con nuovi host di sessione.

## <a name="what-you-need-to-expand-the-host-pool"></a>Cosa è necessario per espandere il pool di host

Prima di iniziare, assicurarsi di aver creato un pool host e le macchine virtuali (VM) host sessione usando uno dei metodi seguenti:

- [Offerta di Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Modello di Azure Resource Manager GitHub](create-host-pools-arm-template.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell-2019.md)

Al momento della creazione delle VM host della sessione e del pool host sono necessarie anche le informazioni seguenti:

- Dimensioni macchina virtuale, immagine e prefisso nome
- Aggiunta a un dominio e credenziali di amministratore tenant di desktop virtuale Windows
- Nome della rete virtuale e della subnet

Le tre sezioni seguenti sono tre metodi che è possibile usare per espandere il pool host. È possibile usare qualsiasi strumento di distribuzione con cui si ha dimestichezza.

>[!NOTE]
>Durante la fase di distribuzione, verranno visualizzati i messaggi di errore per le risorse della macchina virtuale host sessione precedente, se sono attualmente arrestati. Questi errori si verificano perché Azure non può eseguire l'estensione DSC di PowerShell per verificare che le VM host della sessione siano registrate correttamente nel pool host esistente. È possibile ignorare questi errori oppure è possibile evitare gli errori avviando tutte le VM host della sessione nel pool di host esistente prima di avviare il processo di distribuzione.

## <a name="redeploy-from-azure"></a>Ridistribuire da Azure

Se sono già state create le macchine virtuali di un pool host e di una sessione usando l' [offerta di Azure Marketplace](create-host-pools-azure-marketplace-2019.md) o il [modello di Azure Resource Manager di GitHub](create-host-pools-arm-template.md), è possibile ridistribuire lo stesso modello dalla portale di Azure. La ridistribuzione del modello riimmette automaticamente tutte le informazioni immesse nel modello originale, ad eccezione delle password.

Di seguito viene illustrato come ridistribuire il modello di Azure Resource Manager per espandere un pool di host:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Dalla barra di ricerca nella parte superiore della portale di Azure cercare **gruppi di risorse** e selezionare l'elemento in **Servizi**.
3. Trovare e selezionare il gruppo di risorse creato quando è stato creato il pool host.
4. Nel pannello sul lato sinistro del browser selezionare **distribuzioni**.
5. Selezionare la distribuzione appropriata per il processo di creazione del pool host:
     - Se è stato creato il pool di host originale con l'offerta di Azure Marketplace, selezionare la distribuzione a partire da **RDS. Wvd-provision-host-pool**.
     - Se il pool di host originale è stato creato con il modello di Azure Resource Manager GitHub, selezionare la distribuzione denominata **Microsoft. template**.
6. Selezionare **Ridistribuisci**.
     
     >[!NOTE]
     >Se il modello non viene ridistribuito automaticamente quando si seleziona **Ridistribuisci**, selezionare **modello** nel pannello sul lato sinistro del browser, quindi selezionare **Distribuisci**.

7. Selezionare il gruppo di risorse che contiene le VM host della sessione corrente nel pool di host esistente.
     
     >[!NOTE]
     >Se viene visualizzato un errore che indica di selezionare un gruppo di risorse diverso anche se quello immesso è corretto, selezionare un altro gruppo di risorse e quindi selezionare il gruppo di risorse originale.

8. Immettere l'URL seguente per il *_artifactsLocation*:`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Immettere il nuovo numero totale di host di sessione che si desidera includere nel *numero di istanze RDSH*. Se, ad esempio, si espande il pool host da cinque host di sessione a otto, immettere **8**.
10. Immettere la stessa password di dominio esistente utilizzata per il dominio UPN esistente. Non modificare il nome utente, perché in questo caso verrà generato un errore quando si esegue il modello.
11. Immettere la stessa password di amministratore del tenant usata per l'ID dell'utente o dell'applicazione immesso per l' *UPN amministratore tenant o l'ID applicazione*. Ancora una volta, non modificare il nome utente.
12. Completare l'invio per espandere il pool host.

## <a name="run-the-azure-marketplace-offering"></a>Eseguire l'offerta di Azure Marketplace

[Per eseguire il provisioning di un nuovo pool host](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool), seguire le istruzioni in [creare un pool di host usando Azure Marketplace](create-host-pools-azure-marketplace-2019.md) fino a quando non si raggiunge l'offerta di Azure Marketplace. Quando si arriva a questo punto, è necessario immettere le informazioni seguenti per ogni scheda:

### <a name="basics"></a>Nozioni di base

Tutti i valori in questa sezione devono corrispondere a quanto specificato al momento della creazione del pool host e delle VM host della sessione, ad eccezione degli *utenti desktop predefiniti*:

1.    Per *Subscription (sottoscrizione*) selezionare la sottoscrizione in cui è stato creato per la prima volta il pool host.
2.    Per *gruppo di risorse*selezionare lo stesso gruppo di risorse in cui si trovano le VM host della sessione del pool host esistente.
3.    Per *area*selezionare la stessa area in cui si trovano le VM host della sessione del pool host esistente.
4.    Per *nome Hostpool*immettere il nome del pool host esistente.
5.    Per *tipo di desktop*selezionare il tipo di desktop che corrisponde al pool host esistente.
6.    Per *gli utenti desktop predefiniti*, immettere un elenco delimitato da virgole di eventuali utenti aggiuntivi che si desidera accedere ai client desktop virtuali Windows e accedere a un desktop al termine dell'offerta di Azure Marketplace. Ad esempio, se si desidera user3@contoso.com assegnare e user4@contoso.com accedere, immettere user3@contoso.com,.user4@contoso.com
7.    Selezionare **Avanti: Configura macchina virtuale**.

>[!NOTE]
>Ad eccezione degli *utenti desktop predefiniti*, tutti i campi devono corrispondere esattamente a quanto è stato configurato nel pool host esistente. Se è presente una mancata corrispondenza che comporterà un nuovo pool host.

### <a name="configure-virtual-machines"></a>Configurare le macchine virtuali

Tutti i valori dei parametri in questa sezione devono corrispondere a quanto specificato al momento della creazione del pool host e delle VM host della sessione, ad eccezione del numero totale di macchine virtuali. Il numero di macchine virtuali immesse sarà il numero di macchine virtuali nel pool di host espanso:

1. Selezionare le dimensioni della VM corrispondenti alle VM host della sessione esistente.
    
    >[!NOTE]
    >Se le dimensioni specifiche della VM che si cercano non vengono visualizzate nel selettore di dimensioni, significa che non ne è stato ancora eseguito l'onboarding nello strumento di Azure Marketplace. Per richiedere le dimensioni della VM, creare una richiesta oppure aggiornarne una esistente nel [forum UserVoice di Desktop virtuale Windows](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Personalizzare i parametri *profilo di utilizzo*, *Totale utenti*e *numero di macchine virtuali* per selezionare il numero totale di host di sessione che si desidera includere nel pool host. Se, ad esempio, si espande il pool host da cinque host di sessione a otto, configurare queste opzioni per ottenere 8 macchine virtuali.
3. Immettere un prefisso per i nomi delle macchine virtuali. Ad esempio, se si immette il nome "prefisso", le macchine virtuali saranno denominate "prefisso-0", "prefisso-1" e così via.
4. Selezionare **Avanti: impostazioni macchina virtuale**.

### <a name="virtual-machine-settings"></a>Impostazioni della macchina virtuale

Tutti i valori dei parametri in questa sezione devono corrispondere a quanto specificato al momento della creazione del pool host e delle VM host della sessione:

1. Per Image *source* e *Image OS Version*, immettere le stesse informazioni fornite al momento della creazione del pool host.
2. Per l' *aggiunta al dominio di Active Directory UPN* e le password associate, immettere le stesse informazioni fornite al momento della creazione del pool di host per aggiungere le macchine virtuali al dominio Active Directory. Queste credenziali verranno usate per creare un account locale nelle macchine virtuali. È possibile reimpostare questi account locali per modificare le credenziali in un secondo momento.
3. Per informazioni sulle reti virtuali, selezionare la stessa rete virtuale e la stessa subnet per la posizione in cui si trovano le VM host della sessione del pool host esistente.
4. Selezionare **Avanti: Configura informazioni sul desktop virtuale di Windows**.

### <a name="windows-virtual-desktop-information"></a>Informazioni sul desktop virtuale di Windows

Tutti i valori dei parametri in questa sezione devono corrispondere a quanto specificato al momento della creazione del pool host e delle VM host della sessione:

1. Per *Windows Virtual Desktop tenant group name* (Nome gruppo di tenant Desktop virtuale Windows), immettere il nome del gruppo di tenant che contiene il tenant. Lasciare il valore predefinito se non è stato fornito un nome specifico per il gruppo di tenant.
2. Per *Windows Virtual Desktop tenant name* (Nome tenant Desktop virtuale Windows) immettere il nome del tenant in cui verrà creato il pool di host.
3. Specificare le stesse credenziali usate al momento della creazione del pool host e delle VM host della sessione. Se si usa un'entità servizio, immettere l'ID dell'istanza di Azure Active Directory in cui si trova l'entità servizio.
4. Selezionare **Avanti: verifica + crea**.

## <a name="run-the-github-azure-resource-manager-template"></a>Eseguire il modello di Azure Resource Manager di GitHub

Seguire le istruzioni riportate in [eseguire il modello di Azure Resource Manager per il provisioning di un nuovo pool di host](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) e fornire tutti gli stessi valori dei parametri tranne il *numero di istanze RDSH*. Immettere il numero di macchine virtuali host sessione desiderate nel pool host dopo l'esecuzione del modello. Se, ad esempio, si espande il pool host da cinque host di sessione a otto, immettere **8**.

## <a name="next-steps"></a>Passaggi successivi

Ora che il pool host esistente è stato espanso, è possibile accedere a un client desktop virtuale di Windows per testarli come parte di una sessione utente. È possibile connettersi a una sessione con uno dei client seguenti:

- [Connettersi con il client desktop di Windows](../connect-windows-7-and-10.md)
- [Connettersi con il client Web](connect-web-2019.md)
- [Connettersi con il client Android](connect-android-2019.md)
- [Connettersi con il client macOS](connect-macos-2019.md)
- [Connettersi con il client iOS](connect-ios-2019.md)
