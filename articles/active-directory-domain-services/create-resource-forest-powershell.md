---
title: Creare una foresta di risorse Azure AD Domain Services usando Azure PowerShell | Microsoft Docs
description: Questo articolo illustra come creare e configurare una foresta di risorse Azure Active Directory Domain Services e una foresta in uscita in un ambiente di Active Directory Domain Services locale usando Azure PowerShell.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: joflore
ms.openlocfilehash: 32ec3eface215330aba9e40b46e45b97b5c07091
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041105"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Creare una foresta di risorse Azure Active Directory Domain Services e un trust tra foreste in uscita per un dominio locale usando Azure PowerShell

Negli ambienti in cui non è possibile sincronizzare gli hash delle password o in cui gli utenti accedono esclusivamente tramite smart card senza conoscere effettivamente la password, è possibile usare una foresta di risorse in Azure AD Domain Services (Azure AD DS). Una foresta di risorse usa un trust in uscita unidirezionale da Azure AD Domain Services a uno o più ambienti Active Directory Domain Services locali. Questa relazione di trust consente a utenti, applicazioni e computer di eseguire l'autenticazione in un dominio locale dal dominio gestito di Azure AD Domain Services. In una foresta di risorse, gli hash delle password locali non vengono mai sincronizzati.

![Diagramma del trust tra foreste da Azure AD Domain Services ad Active Directory Domain Services locale](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare una foresta di risorse Azure AD DS usando Azure PowerShell
> * Creazione di un trust tra foreste in uscita unidirezionale nel dominio gestito utilizzando Azure PowerShell
> * Configurare DNS in un ambiente AD DS locale per supportare la connettività del dominio gestito
> * Creare un trust tra foreste in ingresso unidirezionale in un ambiente Active Directory Domain Services locale
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> Le foreste di risorse del dominio gestito non supportano attualmente Azure HDInsight o File di Azure. Le foreste utente del dominio gestito predefinite supportano entrambi i servizi aggiuntivi.

## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].

* Installare e configurare Azure PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](/powershell/azure/install-az-ps).
    * Assicurarsi di accedere alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount][Connect-AzAccount].
* Installare e configurare Azure AD PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD](/powershell/azure/active-directory/install-adv2).
    * Assicurarsi di accedere al tenant di Azure usando il cmdlet [Connect-AzureAD][Connect-AzureAD].
* Per abilitare Azure AD DS, sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* Per creare le risorse di Azure AD DS richieste, sono necessari privilegi di *collaboratore* nella sottoscrizione di Azure.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questo articolo viene creato e configurato il trust tra foreste in uscita da un dominio gestito usando il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="deployment-process"></a>Processo di distribuzione

Si tratta di un processo multiparte per la creazione di una foresta di risorse di dominio gestito e la relazione di trust con un servizio di dominio Active Directory locale. I seguenti passaggi generali compilano un ambiente ibrido attendibile:

1. Creare un'entità servizio del dominio gestito.
1. Creare una foresta di risorse di dominio gestito.
1. Creare una connettività di rete ibrida usando una VPN da sito a sito o Express route.
1. Creare il lato dominio gestito della relazione di trust.
1. Creare il lato Active Directory Domain Services locale della relazione di trust.

Prima di iniziare, assicurarsi di comprendere le [considerazioni sulla rete, la denominazione della foresta e i requisiti DNS](tutorial-create-forest-trust.md#networking-considerations). Non è possibile modificare il nome della foresta di domini gestiti dopo che è stato distribuito.

## <a name="create-the-azure-ad-service-principal"></a>Creare l'entità servizio Azure AD

Azure AD DS richiede un'entità servizio sincronizza i dati da Azure AD. Questa entità deve essere creata nel tenant di Azure AD prima della creazione della foresta di risorse del dominio gestito.

Creare un'entità servizio Azure AD per Azure AD DS per comunicare ed eseguire l'autenticazione. Un ID applicazione specifico viene usato denominato *domain controller Services* con ID *6ba9a5d4-8456-4118-B521-9c5ca10cdf84* . Non modificare questo ID applicazione.

Creare un'entità servizio di Azure AD usando il cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal]:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Creare una foresta di risorse di dominio gestito

Per creare una foresta di risorse di dominio gestito, usare lo `New-AzureAaddsForest` script. Questo script fa parte di un set di comandi più ampio che supporta la creazione e la gestione di foreste di risorse del dominio gestito, inclusa la creazione della foresta con binding unidirezionale in una sezione successiva. Questi script sono disponibili dal [PowerShell Gallery](https://www.powershellgallery.com/) e sono firmati digitalmente dal team di progettazione di Azure ad.

1. Per prima cosa, creare un gruppo di risorse usando il cmdlet [New-AzResourceGroup][New-AzResourceGroup] . Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *westus* . Usare il proprio nome e l'area desiderata:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Installare lo `New-AaddsResourceForestTrust` script dalla [PowerShell Gallery][powershell-gallery] usando il cmdlet [Install-script][Install-Script] :

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Esaminare i seguenti parametri necessari per lo `New-AzureAaddsForest` script. Assicurarsi che siano presenti anche i prerequisiti **Azure PowerShell** e **Azure ad** moduli di PowerShell. Assicurarsi di aver pianificato i requisiti della rete virtuale per fornire l'applicazione e la connettività locale.

    | Nome                         | Parametro script          | Descrizione |
    |:-----------------------------|---------------------------|:------------|
    | Subscription                 | *-azureSubscriptionId*    | ID sottoscrizione usato per la fatturazione Azure AD DS. Per ottenere l'elenco delle sottoscrizioni, è possibile usare il cmdlet [Get-AzureRMSubscription][Get-AzureRMSubscription] . |
    | Gruppo di risorse               | *-aaddsResourceGroupName* | Nome del gruppo di risorse per il dominio gestito e le risorse associate. |
    | Location                     | *-aaddsLocation*          | Area di Azure in cui ospitare il dominio gestito. Per le aree disponibili, vedere [aree supportate per Azure AD DS.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Amministratore di Azure AD DS    | *-aaddsAdminUser*         | Nome dell'entità utente del primo amministratore di dominio gestito. Questo account deve essere un account utente cloud esistente nel Azure Active Directory. L'utente e l'utente che esegue lo script vengono aggiunti al gruppo di *amministratori di AAD DC* . |
    | Nome di dominio Azure AD DS      | *-aaddsDomainName*        | FQDN del dominio gestito, in base alle indicazioni precedenti su come scegliere il nome di una foresta. |

    Lo `New-AzureAaddsForest` script può creare la rete virtuale di Azure e Azure ad subnet DS se queste risorse non esistono già. Lo script può facoltativamente creare le subnet del carico di lavoro, se specificato:

    | Nome                              | Parametro script                  | Descrizione |
    |:----------------------------------|:----------------------------------|:------------|
    | Nome della rete virtuale              | *-aaddsVnetName*                  | Nome della rete virtuale per il dominio gestito.|
    | Spazio degli indirizzi                     | *-aaddsVnetCIDRAddressSpace*      | Intervallo di indirizzi della rete virtuale nella notazione CIDR (se si crea la rete virtuale).|
    | Nome della subnet di Azure AD DS           | *-aaddsSubnetName*                | Nome della subnet della rete virtuale *aaddsVnetName* che ospita il dominio gestito. Non distribuire le VM e i carichi di lavoro in questa subnet. |
    | Intervallo di indirizzi Azure AD DS         | *-aaddsSubnetCIDRAddressRange*    | Intervallo di indirizzi della subnet nella notazione CIDR per l'istanza di AAD DS, ad esempio *192.168.1.0/24* . L'intervallo di indirizzi deve essere contenuto nell'intervallo di indirizzi della rete virtuale e diverso da altre subnet. |
    | Nome della subnet del carico di lavoro (facoltativo)   | *-workloadSubnetName*             | Nome facoltativo di una subnet nella rete virtuale *aaddsVnetName* da creare per i carichi di lavoro dell'applicazione. Le macchine virtuali e le applicazioni e anche essere connesse a una rete virtuale di Azure con peering. |
    | Intervallo di indirizzi del carico di lavoro (facoltativo) | *-workloadSubnetCIDRAddressRange* | Intervallo di indirizzi subnet facoltativo nella notazione CIDR per il carico di lavoro dell'applicazione, ad esempio *192.168.2.0/24* . L'intervallo di indirizzi deve essere contenuto nell'intervallo di indirizzi della rete virtuale e diverso da altre subnet.|

1. Creare ora una foresta di risorse di dominio gestito con lo `New-AzureAaaddsForest` script. Nell'esempio seguente viene creata una foresta denominata *addscontoso.com* e viene creata una subnet del carico di lavoro. Specificare i nomi di parametro e gli intervalli di indirizzi IP o le reti virtuali esistenti.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    La creazione della foresta di risorse del dominio gestito e delle risorse di supporto richiede molto tempo. Consente il completamento dello script. Continuare con la sezione successiva per configurare la connettività di rete locale mentre la Azure AD la foresta delle risorse viene sottoposto a provisioning in background.

## <a name="configure-and-validate-network-settings"></a>Configurare e convalidare le impostazioni di rete

Quando il dominio gestito continua a distribuire, configurare e convalidare la connettività di rete ibrida con il Data Center locale. È anche necessaria una macchina virtuale di gestione da usare con il dominio gestito per una manutenzione regolare. Parte della connettività ibrida potrebbe essere già presente nell'ambiente in uso oppure potrebbe essere necessario collaborare con altri utenti del team per configurare le connessioni.

Prima di iniziare, assicurarsi di comprendere le [considerazioni e le raccomandazioni sulla rete](tutorial-create-forest-trust.md#networking-considerations).

1. Creare la connettività ibrida alla rete locale in Azure usando una connessione VPN di Azure o Azure ExpressRoute. La configurazione di rete ibrida esula dall'ambito di questa documentazione e potrebbe essere già presente nell'ambiente in uso. Per informazioni dettagliate su scenari specifici, vedere gli articoli seguenti:

    * [VPN da sito a sito di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * [Panoramica di Azure ExpressRoute](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Se si crea la connessione direttamente alla rete virtuale del dominio gestito, usare una subnet del gateway separata. Non creare il gateway nella subnet del dominio gestito.

1. Per amministrare un dominio gestito, creare una macchina virtuale di gestione, aggiungerla al dominio gestito e installare gli strumenti di gestione di servizi di dominio Active Directory necessari.

    Durante la distribuzione della foresta di risorse del dominio gestito, [creare una macchina virtuale Windows Server](./join-windows-vm.md) e installare gli strumenti di gestione di servizi di dominio [Active Directory di base](./tutorial-create-management-vm.md) per installare gli strumenti di gestione necessari. Attendere per aggiungere la macchina virtuale di gestione al dominio gestito fino a quando non viene distribuito correttamente uno dei passaggi seguenti.

1. Convalidare la connettività di rete tra la rete locale e la rete virtuale di Azure.

    * Verificare che il controller di dominio locale sia in grado di connettersi alla macchina virtuale gestita usando `ping` o desktop remoto, ad esempio.
    * Verificare che la macchina virtuale di gestione sia in grado di connettersi ai controller di dominio locali, usando di nuovo un'utilità, ad esempio `ping` .

1. Nel portale di Azure cercare e selezionare **Azure AD Domain Services** . Scegliere il dominio gestito, ad esempio *aaddscontoso.com* , e attendere che lo stato venga segnalato come **in esecuzione** .

    Quando si esegue, [aggiornare le impostazioni DNS per la rete virtuale di Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) e quindi [abilitare gli account utente per Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) per completare le configurazioni per la foresta delle risorse del dominio gestito.

1. Prendere nota degli indirizzi DNS visualizzati nella pagina Overview. Questi indirizzi sono necessari quando si configura il lato Active Directory locale della relazione di trust in una sezione successiva.
1. Riavviare la macchina virtuale di gestione per ricevere le nuove impostazioni DNS, quindi [aggiungere la macchina virtuale al dominio gestito](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Dopo che la macchina virtuale di gestione è stata aggiunta al dominio gestito, connettersi di nuovo usando desktop remoto.

    Da un prompt dei comandi, usare `nslookup` e il nome della foresta delle risorse del dominio gestito per convalidare la risoluzione dei nomi per la foresta delle risorse.

    ```console
    nslookup aaddscontoso.com
    ```

    Il comando deve restituire due indirizzi IP per la foresta delle risorse.

## <a name="create-the-forest-trust"></a>Creare il trust tra insiemi di strutture

Il trust tra foreste è costituito da due parti: il trust tra foreste in uscita unidirezionale nella foresta delle risorse del dominio gestito e il trust tra foreste in ingresso unidirezionale nella foresta di servizi di dominio Active Directory locale. È possibile creare manualmente entrambi i lati della relazione di trust. Quando vengono creati entrambi i lati, gli utenti e le risorse possono eseguire l'autenticazione con il trust tra foreste. Una foresta di risorse di dominio gestito supporta il trust tra foreste in uscita fino a 5 1 a foreste locali.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Creare il lato dominio gestito della relazione di trust

Utilizzare lo `Add-AaddsResourceForestTrust` script per creare il lato dominio gestito della relazione di trust. Per prima cosa, installare lo `Add-AaddsResourceForestTrust` script dalla [PowerShell Gallery][powershell-gallery] usando il cmdlet [Install-script][Install-Script] :

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

A questo punto specificare lo script per le seguenti informazioni:

| Nome                               | Parametro script     | Descrizione |
|:-----------------------------------|:---------------------|:------------|
| Nome di dominio Azure AD DS            | *-ManagedDomainFqdn* | FQDN del dominio gestito, ad esempio *aaddscontoso.com* |
| Nome di dominio di servizi di dominio Active Directory locale      | *-TrustFqdn*         | Nome di dominio completo della foresta trusted, ad esempio *OnPrem.contoso.com* |
| Nome descrittivo attendibile                | *-TrustFriendlyName* | Nome descrittivo della relazione di trust. |
| Indirizzi IP DNS di servizi di dominio Active Directory locali | *-TrustDnsIPs*       | Elenco delimitato da virgole di indirizzi IPv4 del server DNS per il dominio trusted elencato. |
| Password di attendibilità                     | *-TrustPassword*     | Password complessa per la relazione di trust. Questa password viene immessa anche quando si crea il trust in ingresso unidirezionale in servizi di dominio Active Directory locale. |
| Credenziali                        | *-Credenziali*       | Credenziali usate per l'autenticazione in Azure. L'utente deve essere nel *gruppo AAD DC Administrators* . Se non specificato, lo script richiede l'autenticazione. |

Nell'esempio seguente viene creata una relazione di trust denominata *myAzureADDSTrust* in *OnPrem.contoso.com* . Usare nomi di parametro e password personalizzati:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Ricordare la password di attendibilità. Quando si crea il lato locale del trust, è necessario utilizzare la stessa password.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurare il DNS nel dominio locale

Per risolvere correttamente il dominio gestito dall'ambiente locale, può essere necessario aggiungere server d'inoltro ai server DNS esistenti. Se l'ambiente locale non è stato configurato per comunicare con il dominio gestito, completare i passaggi seguenti da una workstation di gestione per il dominio di AD DS locale:

1. Selezionare **Start | Strumenti di amministrazione | DNS**
1. Fare clic con il pulsante destro del mouse sul server DNS, ad esempio *myAD01* e scegliere **Proprietà**
1. Scegliere **Server d'inoltro** , quindi **Modifica** per aggiungere altri server d'inoltro.
1. Aggiungere gli indirizzi IP del dominio gestito, ad esempio *10.0.1.4* e *10.0.1.5* .
1. Da un prompt dei comandi locale, convalidare la risoluzione dei nomi usando **nslookup** del nome di dominio della foresta di risorse del dominio gestito. Ad esempio, `Nslookup aaddscontoso.com` deve restituire i due indirizzi IP per la foresta di risorse del dominio gestito.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Creare il trust tra foreste in ingresso nel dominio locale

Il dominio locale di AD DS richiede un trust tra foreste in ingresso per il dominio gestito. Il trust deve essere creato manualmente nel dominio di Active Directory Domain Services locale e non può essere creato nel portale di Azure.

Per configurare il trust in ingresso nel dominio Active Directory Domain Services locale, completare i passaggi seguenti da una workstation di gestione per il dominio di Active Directory Domain Services locale:

1. Fare clic sul pulsante **Start | Strumenti di amministrazione | Domini e trust di Active Directory**
1. Fare clic con il pulsante destro del mouse sul dominio, ad esempio *onprem.contoso.com* e scegliere **Proprietà**
1. Scegliere la scheda **Trust** , quindi **Nuova relazione di trust**
1. Immettere il nome del dominio gestito, ad esempio *aaddscontoso.com* , quindi fare clic su **Next (avanti** ).
1. Selezionare l'opzione per creare un **Trust tra foreste** , quindi creare un trust **Unidirezionale: in ingresso** .
1. Scegliere di creare il trust per **Solo questo dominio** . Nel passaggio successivo si creerà il trust nel portale di Azure per il dominio gestito.
1. Scegliere di usare l' **Autenticazione estesa a tutta la foresta** , quindi immettere e confermare una password del trust. Questa stessa password verrà anche immessa nel portale di Azure nella sezione successiva.
1. Per le finestre successive lasciare le opzioni predefinite, quindi scegliere l'opzione **No, non confermare il trust in uscita** . Non è possibile convalidare la relazione di trust perché l'account amministratore delegato per la foresta di risorse del dominio gestito non ha le autorizzazioni necessarie. Questo comportamento dipende dalla progettazione.
1. Selezionare **Finish** (Fine)

## <a name="validate-resource-authentication"></a>Convalidare l'autenticazione delle risorse

Gli scenari comuni seguenti consentono di verificare che il trust tra foreste esegua correttamente l'autenticazione degli utenti e l'accesso alle risorse:

* [Autenticazione utente locale dalla foresta di risorse Azure AD Domain Services](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Accesso alle risorse nella foresta di risorse Azure AD Domain Services usando un utente locale](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Abilitare la condivisione di file e stampanti](#enable-file-and-printer-sharing)
    * [Creare un gruppo di sicurezza e aggiungere membri](#create-a-security-group-and-add-members)
    * [Creare una condivisione file per l'accesso tra foreste](#create-a-file-share-for-cross-forest-access)
    * [Convalidare l'autenticazione tra foreste a una risorsa](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticazione utente locale dalla foresta di risorse Azure AD Domain Services

È necessario che la macchina virtuale Windows Server sia stata aggiunta al dominio delle risorse del dominio gestito. Usare questa macchina virtuale per verificare che l'utente locale possa eseguire l'autenticazione in una macchina virtuale.

1. Connettersi alla macchina virtuale Windows Server aggiunta alla foresta delle risorse del dominio gestito usando Desktop remoto e le credenziali di amministratore di dominio gestito. Se viene ricevuto un errore di Autenticazione a livello di rete (NLA), controllare che l'account utente usato non sia un account utente di dominio.

    > [!TIP]
    > Per connettersi in modo sicuro alle macchine virtuali Unite a Azure AD Domain Services, è possibile usare il [servizio host di Azure Bastion](../bastion/bastion-overview.md) in aree di Azure supportate.

1. Aprire un prompt dei comandi e usare il comando `whoami` per visualizzare il nome distinto dell'utente attualmente autenticato:

    ```console
    whoami /fqdn
    ```

1. Usare il comando `runas` per eseguire l'autenticazione come utente dal dominio locale. Nel comando seguente sostituire `userUpn@trusteddomain.com` con il nome dell'entità utente di un utente dal dominio locale attendibile. Il comando richiede l'immissione della password dell'utente:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se l'autenticazione ha esito positivo, verrà aperto un nuovo prompt dei comandi. Il titolo del nuovo prompt dei comandi include `running as userUpn@trusteddomain.com`.
1. Usare `whoami /fqdn` nel nuovo prompt dei comandi per visualizzare il nome distinto dell'utente autenticato da Active Directory locale.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Accesso alle risorse nella foresta di risorse Azure AD Domain Services usando un utente locale

Usando la macchina virtuale Windows Server aggiunta alla foresta delle risorse del dominio gestito, è possibile testare lo scenario in cui gli utenti possono accedere alle risorse ospitate nella foresta delle risorse quando eseguono l'autenticazione da computer nel dominio locale con gli utenti del dominio locale. Gli esempi seguenti illustrano come creare e testare diversi scenari comuni.

#### <a name="enable-file-and-printer-sharing"></a>Abilitare la condivisione di file e stampanti

1. Connettersi alla macchina virtuale Windows Server aggiunta alla foresta delle risorse del dominio gestito usando Desktop remoto e le credenziali di amministratore di dominio gestito. Se viene ricevuto un errore di Autenticazione a livello di rete (NLA), controllare che l'account utente usato non sia un account utente di dominio.

    > [!TIP]
    > Per connettersi in modo sicuro alle macchine virtuali Unite a Azure AD Domain Services, è possibile usare il [servizio host di Azure Bastion](../bastion/bastion-overview.md) in aree di Azure supportate.

1. Aprire **Impostazioni di Windows** , quindi cercare e selezionare **Centro connessioni di rete e condivisione** .
1. Scegliere l'opzione per **Modifica impostazioni di condivisione avanzate** .
1. In **Profilo di dominio** selezionare **Attiva condivisione file e stampanti** e quindi **Salva modifiche** .
1. Chiudere **Centro connessioni di rete e condivisione** .

#### <a name="create-a-security-group-and-add-members"></a>Creare un gruppo di sicurezza e aggiungere membri

1. Aprire **Utenti e computer di Active Directory** .
1. Fare clic con il pulsante destro del mouse sul nome di dominio, scegliere **Nuovo** , quindi selezionare **Unità organizzativa** .
1. Nella casella del nome digitare *LocalObjects* e quindi selezionare **OK** .
1. Scegliere e fare clic con il pulsante destro del mouse su **LocalObjects** nel riquadro di spostamento. Scegliere **Nuovo** e quindi **Gruppo** .
1. Digitare *FileServerAccess* nella casella **Nome gruppo** . Per **Ambito gruppo** selezionare **Locale dominio** , quindi scegliere **OK** .
1. Nel riquadro del contenuto fare doppio clic su **FileServerAccess** . Selezionare **Membri** , scegliere **Aggiungi** , quindi selezionare **Percorsi** .
1. Selezionare l'istanza di Active Directory locale dalla visualizzazione **Percorsi** , quindi scegliere **OK** .
1. Digitare *Utenti del dominio* nella casella **Immettere i nomi degli oggetti da selezionare** . Selezionare **Controlla nomi** , fornire le credenziali per l'istanza di Active Directory locale, quindi selezionare **OK** .

    > [!NOTE]
    > È necessario fornire le credenziali perché la relazione di trust è unidirezionale. Ciò significa che gli utenti del dominio gestito non possono accedere alle risorse o cercare utenti o gruppi nel dominio trusted (locale).

1. Il gruppo **Utenti del dominio** dell'istanza di Active Directory locale deve essere membro del gruppo **FileServerAccess** . Selezionare **OK** per salvare il gruppo e chiudere la finestra.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Creare una condivisione file per l'accesso tra foreste

1. Nella macchina virtuale Windows Server aggiunta alla foresta delle risorse del dominio gestito creare una cartella e specificare un nome, ad esempio *CrossForestShare* .
1. Fare clic con il pulsante destro del mouse sulla nuova cartella e scegliere **Proprietà** .
1. Selezionare la scheda **Sicurezza** e quindi scegliere **Modifica** .
1. Nella finestra di dialogo *Autorizzazioni per CrossForestShare* selezionare **Aggiungi** .
1. Digitare *FileServerAccess* in **Immettere i nomi degli oggetti da selezionare** , quindi selezionare **OK** .
1. Selezionare *FileServerAccess* dall'elenco **Gruppi o nomi utente** . Nell'elenco **Autorizzazioni per FileServerAccess** scegliere *Consenti* per le autorizzazioni di **modifica** e **scrittura** , quindi selezionare **OK** .
1. Selezionare la scheda **Condivisione** , quindi scegliere **Condivisione avanzata**
1. Scegliere **Condividi la cartella** , quindi immettere un nome facile da ricordare per la condivisione file in **Nome condivisione** , ad esempio *CrossForestShare* .
1. Selezionare **Autorizzazioni** . Nell'elenco **Autorizzazioni per Everyone** scegliere **Consenti** per l'autorizzazione **Modifica** .
1. Selezionare **OK** due volte e quindi **Chiudi** .

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Convalidare l'autenticazione tra foreste a una risorsa

1. Accedere a un computer Windows aggiunto all'istanza di Active Directory locale usando un account utente di Active Directory locale.
1. Con **Esplora risorse** connettersi alla condivisione creata usando il nome host completo e la condivisione, ad esempio `\\fs1.aaddscontoso.com\CrossforestShare`.
1. Per convalidare l'autorizzazione di scrittura, fare clic con il pulsante destro del mouse sulla cartella, scegliere **Nuovo** e quindi selezionare **Documento di testo** . Usare il nome predefinito **Nuovo documento di testo** .

    Se le autorizzazioni di scrittura sono impostate correttamente, verrà creato un nuovo documento di testo. Nei passaggi seguenti verrà quindi aperto, modificato ed eliminato il file in base alle esigenze.
1. Per convalidare l'autorizzazione di lettura, aprire il **Nuovo documento di testo** .
1. Per convalidare l'autorizzazione di modifica, aggiungere testo al file e chiudere **Blocco note** . Quando viene richiesto di salvare le modifiche, scegliere **Salva** .
1. Per convalidare l'autorizzazione di eliminazione, fare clic con il pulsante destro del mouse su **Nuovo documento di testo** e scegliere **Elimina** . Scegliere **Sì** per confermare l'eliminazione del file.

## <a name="update-or-remove-outbound-forest-trust"></a>Aggiornare o rimuovere il trust tra foreste in uscita

Se è necessario aggiornare una foresta in uscita unidirezionale esistente dal dominio gestito, è possibile usare gli `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` script e. Questi script sono utili negli scenari in cui si desidera aggiornare il nome descrittivo trust della foresta o la password di attendibilità. Per rimuovere una relazione di trust unidirezionale in uscita dal dominio gestito, è possibile usare lo `Remove-AaddsResourceForestTrust` script. È necessario rimuovere manualmente il trust tra foreste in ingresso unidirezionale nella foresta di servizi di dominio Active Directory locale associata.

### <a name="update-a-forest-trust"></a>Aggiornare un trust tra foreste

Normalmente, la foresta di domini gestiti e la foresta locale negoziano un normale processo di aggiornamento delle password. Questo fa parte del normale processo di sicurezza delle relazioni di trust di servizi di dominio Active Directory. Non è necessario ruotare manualmente la password di trust, a meno che non si sia verificato un problema nella relazione di trust e si desideri reimpostare manualmente una password nota. Per ulteriori informazioni, vedere la pagina relativa alle [modifiche della password dell'oggetto dominio trusted](concepts-forest-trust.md#tdo-password-changes).

I passaggi di esempio seguenti illustrano come aggiornare una relazione di trust esistente se è necessario reimpostare manualmente la password di trust in uscita:

1. Installare gli `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` script e dal [PowerShell Gallery][powershell-gallery] usando il cmdlet [Install-script][Install-Script] :

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Prima di poter aggiornare un trust esistente, ottenere prima di tutto la risorsa di attendibilità tramite lo `Get-AaddsResourceForestTrusts` script. Nell'esempio seguente il trust esistente viene assegnato a un oggetto denominato *existingTrust* . Specificare il nome della foresta di dominio gestito e il nome della foresta locale da aggiornare:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Per aggiornare la password di trust esistente, usare lo `Set-AaddsResourceForestTrust` script. Specificare l'oggetto trust esistente dal passaggio precedente, quindi una nuova password per la relazione di trust. PowerShell non impone alcuna complessità della password, quindi assicurarsi di generare e usare una password sicura per l'ambiente.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Eliminare un trust tra foreste

Se il trust tra foreste in uscita unidirezionale dal dominio gestito a una foresta di servizi di dominio Active Directory locale non è più necessario, è possibile rimuoverlo. Prima di rimuovere il trust, assicurarsi che non sia necessario eseguire l'autenticazione per le applicazioni o i servizi per la foresta di servizi di dominio Active Directory locale. È necessario rimuovere manualmente la relazione di trust unidirezionale in ingresso nella foresta di servizi di dominio Active Directory locale.

1. Installare lo `Remove-AaddsResourceForestTrust` script dalla [PowerShell Gallery][powershell-gallery] usando il cmdlet [Install-script][Install-Script] :

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. A questo punto, rimuovere il trust tra insiemi di strutture tramite lo `Remove-AaddsResourceForestTrust` script. Nell'esempio seguente viene rimosso il trust denominato *myAzureADDSTrust* tra la foresta di domini gestiti denominata *aaddscontoso.com* e la foresta locale *OnPrem.contoso.com* . Specificare il nome della foresta di dominio gestito e il nome della foresta locale da rimuovere:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Per rimuovere il trust in ingresso unidirezionale dalla foresta di servizi di dominio Active Directory locale, connettersi a un computer di gestione con accesso alla foresta di servizi di dominio Active Directory locale e completare i passaggi seguenti:

1. Fare clic sul pulsante **Start | Strumenti di amministrazione | Domini e trust di Active Directory**
1. Fare clic con il pulsante destro del mouse sul dominio, ad esempio *onprem.contoso.com* e scegliere **Proprietà**
1. Scegliere la scheda **trust** , quindi selezionare il trust in ingresso esistente dalla foresta di domini gestiti.
1. Selezionare **Rimuovi** , quindi confermare che si desidera rimuovere il trust in ingresso.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare una foresta di risorse di dominio gestito usando Azure PowerShell
> * Creazione di un trust tra foreste in uscita unidirezionale nel dominio gestito utilizzando Azure PowerShell
> * Configurare DNS in un ambiente AD DS locale per supportare la connettività del dominio gestito
> * Creare un trust tra foreste in ingresso unidirezionale in un ambiente Active Directory Domain Services locale
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Per informazioni più concettuali sui tipi di foresta in Azure AD Domain Services, vedere [Che cosa sono le foreste di risorse?][concepts-forest] e [Come funzionano i trust tra foreste in Azure Active Directory Domain Services?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/