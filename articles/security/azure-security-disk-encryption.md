---
title: Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux | Documentazione Microsoft
description: Questo articolo offre una panoramica di Crittografia dischi di Microsoft Azure per le VM IaaS Windows e Linux.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: kakhan
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: d2887e255e59c164bb6d733988053f514a118c7b
ms.lasthandoff: 04/15/2017


---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux
Microsoft Azure è caratterizzato dal massimo impegno volto ad assicurare la privacy e la sovranità dei dati e a consentire il controllo dei dati ospitati in Azure con una gamma di tecnologie avanzate per crittografare, controllare e gestire le chiavi di crittografia e controllare e verificare l'accesso ai dati. I clienti di Azure hanno quindi la possibilità di scegliere la soluzione che meglio soddisfa le proprie esigenze aziendali. In questo documento viene introdotta una nuova soluzione tecnologica, "Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux", che facilita la protezione e la salvaguardia dei dati per rispettare gli impegni in termini di sicurezza e conformità dell'organizzazione. Il documento include informazioni dettagliate sull'uso delle funzionalità di crittografia del disco di Azure, compresi gli scenari supportati e le esperienze utente.

> [!NOTE]
> Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione.

## <a name="overview"></a>Panoramica
Crittografia dischi di Azure è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Crittografia dischi di Azure si basa sulla funzionalità [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard di settore disponibile in Windows e sulla funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per offrire la crittografia del volume per i dischi dei dati e del sistema operativo. La soluzione è integrata con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) per consentire di controllare e gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione di Key Vault. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Crittografia dischi di Azure per macchine virtuali IaaS Windows e Linux ha ora **disponibilità generale** in tutte le aree pubbliche e AzureGov di Azure per macchine virtuali standard e macchine virtuali con archiviazione Premium.

### <a name="encryption-scenarios"></a>Scenari di crittografia
La soluzione Crittografia dischi di Azure supporta i tre scenari dei clienti descritti di seguito:

* Abilitare la crittografia nelle nuove VM IaaS create da chiavi di crittografia e VHD pre-crittografati
* Abilitare la crittografia in nuove VM IaaS create da immagini della Raccolta di Azure
* Abilitare la crittografia in VM IaaS esistenti in esecuzione in Azure
* Disabilitare la crittografia nelle VM IaaS Windows
* Disabilitare la crittografia nelle unità dati per le VM IaaS Linux
* Abilitare la crittografia delle macchine virtuali con disco gestito
* Aggiornare le impostazioni di crittografia di una macchina virtuale non dotata di archiviazione Premium crittografata esistente
* Backup e ripristino di macchine virtuali crittografate con chiave di crittografia della chiave

La soluzione supporta gli scenari seguenti per le macchine virtuali IaaS, se abilitati in Microsoft Azure:

* Integrazione dell'insieme di credenziali delle chiavi di Azure.
* Macchine virtuali di livello Standard: [serie A, D, DS, G, GS, F e così via per VM IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Abilitare la crittografia nelle macchine virtuali IaaS Windows e Linux e nelle macchine virtuali con disco gestito
* Disabilitare la crittografia nel sistema operativo e nelle unità dati per le macchine virtuali IaaS Windows e per le macchine virtuali con disco gestito
* Disabilitare la crittografia nelle unità dati per le macchine virtuali IaaS Linux e per le macchine virtuali con disco gestito
* Abilitare la crittografia in VM IaaS eseguite nel sistema operativo client Windows
* Abilitare la crittografia su volumi con percorsi di montaggio
* Abilitare la crittografia nelle macchine virtuali Linux configurate con striping del disco (RAID) tramite mdadm
* Abilitare la crittografia nelle macchine virtuali Linux usando LVM per i dischi dati
* Abilitare la crittografia nelle VM Windows configurate con spazi di archiviazione
* Aggiornare le impostazioni di crittografia di una macchina virtuale non dotata di archiviazione Premium crittografata esistente
* Sono supportate tutte le aree di Azure pubbliche e AzureGov

La soluzione non supporta gli scenari, le funzionalità e la tecnologia seguenti:

* VM IaaS del piano Basic
* Disabilitazione della crittografia in un'unità del sistema operativo per le VM IaaS Linux
* Macchine virtuali IaaS create usando il metodo di creazione classico per le macchine virtuali
* Integrazione con il servizio di gestione delle chiavi locale.
* File di Azure (file system condiviso), file system di rete (NFS, Network File System), volumi dinamici e macchine virtuali Windows configurate con sistemi RAID basati su software
* Backup e ripristino di macchine virtuali crittografate senza chiave di crittografia della chiave.
* Aggiornare le impostazioni di crittografia di una macchina virtuale con archiviazione Premium crittografata esistente.

> [!NOTE]
> Il backup e il ripristino delle macchine virtuali crittografate sono supportati solo per le VM crittografate con la configurazione della chiave di crittografia della chiave. Non sono supportati nelle macchine virtuali crittografate senza chiave di crittografia della chiave. La chiave di crittografia della chiave è un parametro facoltativo che abilita la crittografia delle VM. Questo supporto sarà presto disponibile.
> L'aggiornamento delle impostazioni di crittografia di una macchina virtuale con archiviazione Premium crittografata esistente non è supportato. Questo supporto sarà presto disponibile.

### <a name="encryption-features"></a>Funzionalità di crittografia
Quando si abilita e si distribuisce la Crittografia dischi di Azure per le VM IaaS di Azure, sono abilitate le funzionalità seguenti, a seconda della configurazione fornita:

* Crittografia del volume del sistema operativo per proteggere il volume di avvio inattivo nella risorsa di archiviazione dell'utente
* Crittografia dei volumi dati per proteggere i volumi dati inattivi nella risorsa di archiviazione dell'utente
* Disabilitazione della crittografia nel sistema operativo e nelle unità dati per le VM IaaS Windows
* Disabilitazione della crittografia nelle unità dati per le macchine virtuali IaaS Linux
* Protezione delle chiavi di crittografia e dei segreti nella sottoscrizione di Key Vault
* Segnalazione dello stato di crittografia della macchina virtuale IaaS crittografata
* Rimozione delle impostazioni di configurazione della crittografia del disco dalla macchina virtuale IaaS
* Backup e ripristino delle macchine virtuali crittografate usando il servizio Backup di Azure

> [!NOTE]
> Il backup e il ripristino delle macchine virtuali crittografate sono supportati solo per le VM crittografate con la configurazione della chiave di crittografia della chiave. Non sono supportati nelle macchine virtuali crittografate senza chiave di crittografia della chiave. La chiave di crittografia della chiave è un parametro facoltativo che abilita la crittografia delle VM.

Crittografia dischi di Azure per macchine virtuali IaaS per soluzioni Windows e Linux include:

* Estensione di crittografia del disco per Windows.
* Estensione di crittografia del disco per Linux.
* Cmdlet di PowerShell per la crittografia del disco.
* Cmdlet dell'interfaccia della riga di comando di Azure per la crittografia del disco.
* Modelli di Azure Resource Manager per la crittografia del disco.

La soluzione Crittografia dischi di Azure è supportata nelle macchine virtuali IaaS in esecuzione in sistemi operativi Windows o Linux. Per altre informazioni sui sistemi operativi supportati, vedere la sezione "Prerequisiti".

> [!NOTE]
> Non è previsto alcun addebito aggiuntivo per la crittografia dei dischi delle macchine virtuali con Crittografia dischi di Azure.

### <a name="value-proposition"></a>Proposta di valore
Quando si applica la soluzione di gestione Crittografia dischi di Azure, è possibile soddisfare le esigenze aziendali seguenti:

* Le VM IaaS inattive sono protette perché è possibile usare la tecnologia di crittografia standard per soddisfare i requisiti di sicurezza e conformità dell'organizzazione.
* Le macchine virtuali IaaS vengono avviate con chiavi e criteri controllati dai clienti ed è possibile controllarne l'utilizzo nell'insieme di credenziali delle chiavi.

### <a name="encryption-workflow"></a>Flusso di lavoro della crittografia
Per abilitare la crittografia dei dischi per macchine virtuali Windows e Linux, seguire questa procedura:

1. Scegliere uno scenario di crittografia tra gli scenari di crittografia precedenti.
2. Acconsentire esplicitamente per abilitare la crittografia dei dischi tramite il modello di Resource Manager di Crittografia dischi di Azure, i cmdlet di PowerShell o un comando dell'interfaccia della riga di comando e specificare la configurazione della crittografia.

   * Per lo scenario di disco rigido virtuale con crittografia del cliente, caricare il disco rigido virtuale crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Specificare quindi la configurazione della crittografia per abilitare la crittografia in una nuova macchina virtuale IaaS.
   * Per le nuove macchine virtuali create dal Marketplace e per le macchine virtuali esistenti già in esecuzione in Azure, specificare la configurazione della crittografia per abilitare la crittografia nella VM IaaS.

3. Concedere l'accesso alla piattaforma Azure per la lettura del materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per i sistemi Windows e Passphrase per Linux, dall'insieme di credenziali delle chiavi locale per abilitare la crittografia nella VM IaaS.

4. Specificare l'identità dell'applicazione di Azure Active Directory (Azure AD) per scrivere il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi. Questa operazione abilita la crittografia nella macchina virtuale IaaS per gli scenari indicati nel passaggio 2.

5. Azure aggiorna il modello del servizio della macchina virtuale con la crittografia e la configurazione dell'insieme di credenziali delle chiavi e quindi configura la VM crittografata.

 ![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Flusso di lavoro della decrittografia
Per disabilitare la crittografia dei dischi per le macchine virtuali IaaS, seguire questa procedura generale:

1. Scegliere di disabilitare la crittografia (decrittografia) in una macchina virtuale IaaS in esecuzione in Azure tramite il modello di Resource Manager per Crittografia dischi di Azure o i cmdlet di PowerShell, quindi specificare la configurazione della crittografia.

 Questo passaggio agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione. Come indicato nella sezione precedente, tuttavia, la disabilitazione della crittografia del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux.
2. Azure aggiorna il modello di servizi della macchina virtuale e la VM IaaS viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato.

> [!NOTE]
> L'operazione di disabilitazione della crittografia non elimina l'insieme di credenziali delle chiavi e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per sistemi Windows o Passphrase per Linux.
 > La disabilitazione della crittografica del disco del sistema operativo per Linux non è supportata. Il passaggio di decrittografia è consentito solo per le unità dati nelle macchine virtuali Linux.

## <a name="prerequisites"></a>Prerequisiti
Prima di abilitare Crittografia dischi di Azure nelle macchine virtuali IaaS di Azure per gli scenari supportati illustrati nella sezione "Panoramica", vedere i prerequisiti seguenti:

* Per creare le risorse in Azure nella aree geografiche supportate, è necessario avere una sottoscrizione di Azure attiva e valida.
* Crittografia dischi di Azure è supportata nelle versioni di Windows Server seguenti: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.
* Crittografia dischi di Azure è supportato nelle versioni client di Windows seguenti: client Windows 8 e client Windows 10.

> [!NOTE]
> Per Windows Server 2008 R2 è necessario che .NET Framework 4.5 sia installato prima dell'abilitazione della crittografia in Azure. È possibile installarlo da Windows Update tramite l'aggiornamento facoltativo Microsoft .NET Framework 4.5.2 per i sistemi Windows Server 2008 R2 basati su x64 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Crittografia dischi di Azure è supportata nelle distribuzioni e versioni del server Linux seguenti:

| Distribuzione Linux | Versione | Tipo di volume supportato per la crittografia|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Disco del sistema operativo e dati |
| Ubuntu | 14.04.5-DAILY-LTS | Disco del sistema operativo e dati |
| Ubuntu | 12.10 | Disco dati |
| Ubuntu | 12.04 | Disco dati |
| RHEL | 7.3 | Disco del sistema operativo e dati |
| RHEL | 7,2 | Disco del sistema operativo e dati |
| RHEL | 6.8 | Disco del sistema operativo e dati |
| RHEL | 6.7 | Disco dati |
| CentOS | 7.3 | Disco del sistema operativo e dati |
| CentOS | 7.2n | Disco del sistema operativo e dati |
| CentOS | 6.8 | Disco del sistema operativo e dati |
| CentOS | 7.1 | Disco dati |
| CentOS | 7.0 | Disco dati |
| CentOS | 6.7 | Disco dati |
| CentOS | 6.6 | Disco dati |
| CentOS | 6,5 | Disco dati |
| openSUSE | 13.2 | Disco dati |
| SLES | 12 SP1 | Disco dati |
| SLES | 12-SP1 (Premium) | Disco dati |
| SLES | HPC 12 | Disco dati |
| SLES | 11-SP4 (Premium) | Disco dati |
| SLES | 11 SP4 | Disco dati |

* Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area e sottoscrizione di Azure.

> [!NOTE]
> La configurazione delle risorse in aree separate causa un errore nell'attivazione della funzionalità Crittografia dischi di Azure.

* Per installare e configurare l'insieme di credenziali delle chiavi per Crittografia dischi di Azure, vedere la sezione **Installare e configurare l'insieme di credenziali delle chiavi per Crittografia dischi di Azure** nella sezione *Prerequisiti* di questo articolo.
* Per impostare e configurare l'applicazione Azure AD in Azure Active Directory per Crittografia dischi di Azure, vedere la sezione **Installare l'applicazione Azure AD in Azure Active Directory** nella sezione *Prerequisiti* di questo articolo.
* Per impostare e configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione Azure AD, vedere la sezione **Configurare i criteri di accesso per l'insieme di credenziali delle chiavi per l'applicazione Azure AD** nella sezione *Prerequisiti* di questo articolo.
* Per preparare un disco rigido virtuale Windows pre-crittografato, vedere la sezione **Preparare un disco rigido virtuale Windows pre-crittografato** nell'*Appendice*.
* Per preparare un disco rigido virtuale Linux pre-crittografato, vedere la sezione **Preparare un disco rigido virtuale Linux pre-crittografato** nell'*Appendice*.
* La piattaforma Azure deve avere accesso alle chiavi di crittografia o ai segreti nell'insieme di credenziali delle chiavi per renderli disponibili alla macchina virtuale in fase di avvio e di decrittografia del volume del sistema operativo della macchina virtuale. Per concedere autorizzazioni alla piattaforma Azure, impostare la proprietà **EnabledForDiskEncryption** nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere **Installare e configurare l'insieme di credenziali delle chiavi per Crittografia dischi di Azure** nell'Appendice.
* È necessario applicare il controllo delle versioni agli URL del segreto dell'insieme di credenziali delle chiavi e della chiave di crittografia della chiave. Azure applica questa restrizione relativa al controllo delle versioni. Per informazioni sugli URL del segreto e della chiave di crittografia della chiave validi, vedere gli esempi seguenti:

  * Esempio di URL segreto valido:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Esempio di URL della chiave di crittografia della chiave valido:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Crittografia dischi di Azure non supporta la possibilità di specificare i numeri di porta come parte dei segreti dell'insieme di credenziali delle chiavi e degli URL della chiave di crittografia della chiave. Ecco alcuni esempi di URL di insiemi di credenziali delle chiavi non supportati e supportati:

  * URL dell'insieme di credenziali delle chiavi non accettabile:  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL dell'insieme di credenziali delle chiavi accettabile:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Per abilitare la funzionalità Crittografia dischi di Azure, le VM IaaS devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:
  * Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi a un endpoint di Azure Active Directory, \[Login.windows.net\].
  * Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  * La VM IaaS deve potersi connettere a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e a un account di archiviazione di Azure che ospita i file del disco rigido virtuale.

  > [!NOTE]
  > Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP.
  >
  >Per configurare e accedere ad Azure Key Vault dietro un firewall (https://docs.microsoft.com/en-us/azure/key-vault/key-vault-access-behind-firewall)

* Usare la versione più recente della versione di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Crittografia dischi di Azure non è supportata in [Azure PowerShell SDK versione 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Se viene visualizzato un errore relativo all'uso di Azure PowerShell 1.1.0, vedere [Azure Disk Encryption Error Related to Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx) (Errore di Crittografia dischi di Azure correlato ad Azure PowerShell 1.1.0).

* Per eseguire uno dei comandi dell'interfaccia della riga di comando di Azure e associarla alla sottoscrizione di Azure, è necessario installare prima di tutto l'interfaccia della riga di comando di Azure:
  * Per installare l'interfaccia della riga di comando di Azure e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md).
  * Per usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager, vedere [Comandi dell'interfaccia della riga di comando Azure in modalità Resource Manager](../virtual-machines/azure-cli-arm-commands.md).

* È necessario usare il parametro -skipVmBackup quando si usa il cmdlet PS di Crittografia dischi di Azure Set-AzureRmVMDiskEncryptionExtension o il comando CLI per abilitare la crittografia sulla macchina virtuale con disco gestito Azure Managed Disk VM.
> [!NOTE]
 > Se non si specifica il parametro -skipVmBackup, il passaggio di abilitazione della crittografia avrà esito negativo.

* La soluzione Crittografia dischi di Azure usa la protezione con chiave esterna BitLocker per macchine virtuali IaaS Windows. Per le macchine virtuali sono aggiunte a un dominio, NON eseguire il push di criteri di gruppo che applichino protezioni TPM. Per informazioni sui Criteri di gruppo per consentire BitLocker senza un TPM compatibile, vedere [BitLocker Group Policy Reference](https://technet.microsoft.com/library/ee706521) (Informazioni di riferimento sui Criteri di gruppo BitLocker).
* Per creare un'applicazione Azure AD, creare un insieme di credenziali delle chiavi o configurare un insieme di credenziali delle chiavi esistente e abilitare la crittografia, vedere lo [script di PowerShell prerequisito per Crittografia dischi di Azure](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Per configurare i prerequisiti della crittografia dei dischi usando l'interfaccia della riga di comando, vedere [questo script Bash](https://github.com/ejarvi/ade-cli-getting-started).
* Per usare il servizio Backup di Azure per eseguire il backup e ripristinare le macchine virtuali crittografate, quando la crittografia è abilitata con Crittografia dischi di Azure, crittografare le VM usando la configurazione delle chiavi di Crittografia dischi di Azure. Il servizio Backup supporta le macchine virtuali crittografate usando solo la configurazione della chiave di crittografia della chiave. Vedere [Come eseguire il backup e il ripristino delle macchine virtuali crittografate con il Backup di Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).

> [!NOTE]
> Il backup e il ripristino delle macchine virtuali crittografate sono supportati solo per le VM crittografate con la configurazione della chiave di crittografia della chiave. Non sono supportati nelle macchine virtuali crittografate senza chiave di crittografia della chiave. La chiave di crittografia della chiave è un parametro facoltativo che abilita la macchina virtuale.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Configurare l'applicazione Azure AD in Azure Active Directory
Quando è necessario abilitare la crittografia in una macchina virtuale in esecuzione in Azure, Crittografia dischi di Azure genera e scrive le chiavi di crittografia nell'insieme di credenziali delle chiavi. La gestione delle chiavi di crittografia nell'insieme di credenziali delle chiavi richiede l'autenticazione di Azure AD.

Creare quindi un'applicazione Azure AD per questo scopo. Per informazioni dettagliate sulla procedura di registrazione di un'applicazione, vedere la sezione relativa al recupero di un'identità per l'applicazione del post di blog [Azure Key Vault - Step by Step](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) (Procedura dettagliata per Azure Key Vault). Questo post include anche alcuni esempi utili per l'installazione e la configurazione dell'insieme di credenziali delle chiavi. Ai fini dell'autenticazione, è possibile usare l'autenticazione basata sul segreto client o l'autenticazione di Azure AD basata sul certificato client.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Autenticazione basata su segreto client per Azure AD
Le sezioni seguenti sono utili per configurare l'autenticazione basata sul segreto client per Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Creare un'applicazione Azure AD usando Azure PowerShell
Usare il cmdlet di PowerShell seguenti per creare un'applicazione Azure AD:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId è l'ID client di Azure AD e $aadClientSecret è il segreto client da usare in seguito per abilitare Crittografia dischi di Azure. Proteggere correttamente il segreto client di Azure AD.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-classic-portal"></a>Configurazione dell'ID client e del segreto client di Azure AD dal portale di Azure classico
È anche possibile configurare l'ID e il segreto del client di Azure AD usando il [portale di Azure classico]( https://manage.windowsazure.com). Per eseguire questa operazione, seguire questa procedura:

1. Fare clic sulla scheda **Active Directory**.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. Fare clic su **Aggiungi applicazione**, quindi immettere il nome dell'applicazione.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Fare clic sul pulsante con la freccia, quindi configurare le proprietà dell'applicazione.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Fare clic sul segno di spunta nell'angolo in basso a sinistra per completare l'operazione. Viene visualizzata la pagina di configurazione dell'applicazione e l'ID client di Azure AD è disponibile nella parte inferiore della pagina.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. Per salvare il segreto client di Azure AD usare il pulsante **Salva**. Si noti che il segreto client di Azure AD nella casella di testo delle chiavi. Proteggerlo correttamente.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)

 > [!NOTE]
 > Il flusso precedente non è supportato nel portale di Azure classico.

##### <a name="use-an-existing-application"></a>Usare un'applicazione esistente
Per eseguire i comandi seguenti, ottenere e usare il [modulo Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> I comandi seguenti devono essere eseguiti da una nuova finestra di PowerShell. Non usare Azure PowerShell o la finestra di Azure Resource Manager per eseguire questi comandi. È consigliabile usare questo approccio perché questi cmdlet sono disponibili nel modulo MSOnline o in Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Autenticazione basata su certificato per Azure AD
> [!NOTE]
> L'autenticazione basata su certificato per Azure AD non è attualmente supportata nelle VM Linux.

Le sezioni seguenti illustrano come configurare l'autenticazione basata su certificato per Azure AD.

##### <a name="create-an-azure-ad-application"></a>Creare un'applicazione Azure AD
Per creare un'applicazione Azure AD, eseguire i cmdlet di PowerShell seguenti:

> [!NOTE]
> Sostituire la stringa `yourpassword` seguente con la password sicura e proteggere la password.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Dopo avere completato questo passaggio, caricare un file PFX nell'insieme di credenziali delle chiavi e abilitare i criteri di accesso necessari per distribuire il certificato a una VM.

##### <a name="use-an-existing-azure-ad-application"></a>Usare un'applicazione Azure AD esistente
Se si configura l'autenticazione basata su certificato per un'applicazione esistente, usare i cmdlet di PowerShell seguenti. Assicurarsi di eseguirli in una nuova finestra di PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Dopo avere completato questo passaggio, caricare un file PFX nell'insieme di credenziali delle chiavi e abilitare i criteri di accesso necessari per distribuire il certificato a una VM.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Caricare un file PFX nell'insieme di credenziali delle chiavi
Per una spiegazione dettagliata di questo processo, vedere [The Official Azure Key Vault Team Blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) (Blog ufficiale del team di Azure Key Vault). I cmdlet di PowerShell seguenti sono comunque tutto ciò che serve per questa attività. Accertarsi di eseguirli dalla console di Azure PowerShell.

> [!NOTE]
> Sostituire la stringa `yourpassword` seguente con la password sicura e proteggere la password.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Distribuire un certificato nell'insieme di credenziali delle chiavi in una VM esistente
Al termine del caricamento del file PFX, distribuire un certificato disponibile nell'insieme di credenziali delle chiavi in una VM esistente con il codice seguente:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Configurare i criteri di accesso per l'insieme di credenziali delle chiavi per l'applicazione Azure AD
L'applicazione Azure AD deve avere i diritti di accesso alle chiavi o ai segreti nell'insieme di credenziali. Usare il cmdlet [`Set-AzureKeyVaultAccessPolicy`](https://msdn.microsoft.com/library/azure/dn903607.aspx) per concedere le autorizzazioni all'applicazione con l'ID client, generato al momento della registrazione dell'applicazione, come valore del parametro _–ServicePrincipalName_. Per altre informazioni, vedere il post di blog [Azure Key Vault - Step by Step](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) (Procedura dettagliata per Azure Key Vault). Ecco un esempio dell'esecuzione di questa attività tramite PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Crittografia dischi di Azure richiede la configurazione di criteri di accesso all'applicazione client di Azure AD, ovvero le autorizzazioni _WrapKey_ e _Set_.

## <a name="terminology"></a>Terminologia
Per informazioni su alcuni dei termini comuni usati da questa tecnologia, vedere la tabella terminologica seguente:

| Terminologia | Definizione |
| --- | --- |
| Azure AD | Azure AD è l'abbreviazione di [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Un account Azure AD è un prerequisito per le operazioni di autenticazione, archiviazione e recupero dei segreti da un insieme di credenziali delle chiavi. |
| Azure Key Vault | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards), che consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Key Vault](https://azure.microsoft.com/services/key-vault/). |
| ARM | Gestione risorse di Azure |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta nel settore, usata per abilitare la crittografia dei dischi nelle macchine virtuali IaaS di Windows. |
| BEK | Le chiavi di crittografia BitLocker vengono usate per crittografare il volume di avvio del sistema operativo e i volumi dati. Le chiavi BitLocker sono protette nell'insieme di credenziali delle chiavi come segreti. |
| CLI | Vedere [Interfaccia della riga di comando di Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) è il sottosistema di crittografia del disco trasparente basato su Linux usato per abilitare la crittografia del disco nelle macchine virtuali IaaS Linux. |
| KEK | La chiave di crittografia della chiave (KEK, Key Encryption Key) è la chiave asimmetrica (RSA 2048) che è possibile usare per proteggere o per eseguire il wrapping del segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlet PS | Vedere [Azure PowerShell cmdlets](/powershell/azureps-cmdlets-docs) (Cmdlet di Azure PowerShell). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Installare e configurare l'insieme di credenziali delle chiavi per Crittografia dischi di Azure
Crittografia dischi di Azure consente di proteggere le chiavi di crittografia e i segreti dei dischi nell'insieme di credenziali delle chiavi. Per configurare l'insieme di credenziali delle chiavi per Crittografia dischi di Azure, completare i passaggi in ogni sezione seguente.

#### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Per creare un insieme di credenziali delle chiavi, usare una delle opzioni seguenti:

* [Modello di Resource Manager "101-Key-Vault-Create"](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Azure PowerShell key vault cmdlets](https://msdn.microsoft.com/library/dn868052.aspx) (Cmdlet dell'insieme di credenziali delle chiavi di Azure PowerShell)
* Gestione risorse di Azure
* Come [proteggere l'insieme di credenziali delle chiavi](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Se è già stato configurato un insieme di credenziali delle chiavi per la sottoscrizione, passare alla sezione successiva.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Configurare una chiave di crittografia della chiave (facoltativo)
Se si vuole usare una chiave di crittografia della chiave per un livello aggiuntivo di sicurezza per le chiavi di crittografia BitLocker aggiungere una chiave di crittografia della chiave all'insieme di credenziali delle chiavi. Usare il cmdlet [`Add-AzureKeyVaultKey`](https://msdn.microsoft.com/library/dn868048.aspx) per creare una chiave di crittografia della chiave nell'insieme di credenziali delle chiavi. È anche possibile importare una chiave di crittografia della chiave dal modulo di protezione hardware di gestione delle chiavi locale. Per altre informazioni, vedere la [Documentazione su Key Vault](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

È possibile aggiungere la chiave di crittografia della chiave passando ad Azure Resource Manager o usando l'interfaccia di Key Vault.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Configurare le autorizzazioni dell'insieme di credenziali delle chiavi
La piattaforma Azure deve avere accesso alle chiavi di crittografia o i segreti nell'insieme di credenziali delle chiavi per renderli disponibili alla macchina virtuale per l'avvio e la decrittografia dei volumi. Per concedere autorizzazioni alla piattaforma Azure, configurare la proprietà **EnabledForDiskEncryption** nell'insieme di credenziali delle chiavi usando il cmdlet di PowerShell relativo all'insieme di credenziali delle chiavi:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

È anche possibile configurare la proprietà **EnabledForDiskEncryption** in [Azure Resource Explorer](https://resources.azure.com) (Esplora risorse di Azure).

Come indicato in precedenza, è necessario configurare la proprietà **EnabledForDiskEncryption** nell'insieme di credenziali delle chiavi. In caso contrario, la distribuzione avrà esito negativo.

È possibile configurare i criteri di accesso per l'applicazione Azure AD dall'interfaccia di Key Vault:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Nella scheda **Criteri di accesso avanzati** assicurarsi che l'insieme di credenziali delle chiavi sia abilitato per Crittografia dischi di Azure:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Scenari di distribuzione della crittografia dei dischi ed esperienze utente
È possibile abilitare molti scenari di crittografia dei dischi e la procedura può variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Abilitare la crittografia nelle nuove macchine virtuali IaaS create dal Marketplace
È possibile abilitare la crittografia dei dischi nella nuova macchina virtuale IaaS Windows dal Marketplace in Azure usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. Nel modello di avvio rapido di Azure fare clic su **Distribuisci in Azure**, immettere la configurazione della crittografia nel pannello **Parametri**, quindi fare clic su **OK**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le note legali e il contratto, quindi fare clic su **Crea** per abilitare la crittografia in una nuova macchina virtuale IaaS.

> [!NOTE]
> Questo modello crea una nuova VM Windows crittografata che usa l'immagine della raccolta di Windows Server 2012.

È possibile abilitare la crittografia dei dischi in una nuova macchina virtuale IaaS RedHat Linux 7.2 con una matrice RAID-0 da 200 GB usando questo modello di [Resource Manager](https://aka.ms/fde-rhel). Dopo la distribuzione del modello, verificare lo stato della crittografia della macchina virtuale usando il cmdlet `Get-AzureRmVmDiskEncryptionStatus`, come descritto in [Crittografia dell'unità del sistema operativo in una VM Linux in esecuzione](#encrypting-os-drive-on-a-running-linux-vm). Quando la macchina virtuale restituisce uno stato _VMRestartPending_, riavviarla.

La tabella seguente elenca i parametri del modello di Resource Manager per lo scenario con nuove macchine virtuali dal Marketplace con ID client di Azure AD:

| Parametro | Descrizione |
| --- | --- |
| adminUserName | Nome utente dell'amministratore per la macchina virtuale. |
| adminPassword | Password utente dell'amministratore per la macchina virtuale. |
| newStorageAccountName | Nome dell'account di archiviazione per archiviare i dischi rigidi virtuali di dati e del sistema operativo. |
| vmSize | Dimensioni della macchina virtuale. Attualmente sono supportate solo le serie A, D e G Standard. |
| virtualNetworkName | Nome della rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| subnetName | Nome della subnet nella rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultURL | URL dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata (facoltativo). |
| keyVaultResourceGroup | Gruppo di risorse dell'insieme di credenziali delle chiavi. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |

> [!NOTE]
> _KeyEncryptionKeyURL_ è un parametro facoltativo. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per la protezione aggiuntiva della chiave DEK (segreto passphrase) nell'insieme di credenziali delle chiavi.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Abilitare la crittografia delle nuove VM IaaS create da chiavi di crittografia e dischi rigidi virtuali crittografati dei clienti
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in modo più dettagliato il modello di Resource Manager e i comandi dell'interfaccia della riga di comando.

Seguire le istruzioni di una di queste sezioni per la preparazione di immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Windows pre-crittografato](#preparing-a-pre-encrypted-windows-vhd)
* [Preparare un disco rigido virtuale Linux pre-crittografato](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Uso del modello di Resource Manager
È possibile abilitare la crittografia dei dischi nel disco rigido virtuale crittografato usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Nel modello di avvio rapido di Azure fare clic su **Distribuisci in Azure**, immettere la configurazione della crittografia nel pannello **Parametri**, quindi fare clic su **OK**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le note legali e il contratto, quindi fare clic su **Crea** per abilitare la crittografia nella nuova macchina virtuale IaaS.

La tabella seguente elenca i parametri del modello di Resource Manager per il disco rigido virtuale crittografato:

| Parametro | Descrizione |
| --- | --- |
| newStorageAccountName | Nome dell'account di archiviazione per archiviare il disco rigido virtuale del sistema operativo. È necessario che questo account di archiviazione sia già stato creato nello stesso gruppo di risorse e nello stesso percorso della VM. |
| osVhdUri | URI del disco rigido virtuale del sistema operativo dall'account di archiviazione. |
| osType | Tipo di prodotto del sistema operativo (Windows/Linux). |
| virtualNetworkName | Nome della rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. È necessario che il nome sia già stato creato nello stesso gruppo di risorse e nello stesso percorso della VM. |
| subnetName | Nome della subnet nella rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| vmSize | Dimensioni della macchina virtuale. Attualmente sono supportate solo le serie A, D e G Standard. |
| keyVaultResourceID | ResourceID che identifica la risorsa dell'insieme di credenziali delle chiavi in Azure Resource Manager. È possibile ottenerlo usando il cmdlet `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId` di PowerShell. |
| keyVaultSecretUrl | URL della chiave di crittografia dei dischi configurato nell'insieme di credenziali delle chiavi. |
| keyVaultKekUrl | URL della chiave di crittografia della chiave per crittografare la chiave di crittografia dei dischi generata. |
| vmName | Nome della VM IaaS. |

#### <a name="using-powershell-cmdlets"></a>Usare i cmdlet PowerShell
È possibile abilitare la crittografia dei dischi nel disco rigido virtuale crittografato usando il cmdlet [`Set-AzureRmVMOSDisk`](https://msdn.microsoft.com/library/azure/mt603746.aspx) di PowerShell.  

#### <a name="using-cli-commands"></a>Uso dei comandi dell'interfaccia della riga di comando
Eseguire queste operazioni per abilitare la crittografia dei dischi per questo scenario con i comandi dell'interfaccia della riga di comando:

1. Impostare criteri di accesso per l'insieme di credenziali delle chiavi:

   * Impostare il flag **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Configurare le autorizzazioni per l'applicazione Azure AD per scrivere segreti nell'insieme di credenziali delle chiavi:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Per abilitare la crittografia in una VM esistente o in esecuzione, digitare:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Ottenere lo stato della crittografia:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Per abilitare la crittografia in una nuova macchina virtuale dal disco rigido virtuale crittografato, usare i parametri seguenti con il comando `azure vm create`:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Abilitare la crittografia in una VM IaaS Windows esistente o in esecuzione in Azure
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in modo più dettagliato come abilitarla usando il modello di Resource Manager e i comandi dell'interfaccia della riga di comando.

#### <a name="using-the-resource-manager-template"></a>Uso del modello di Resource Manager
È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Windows esistenti o in esecuzione usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. Nel modello di avvio rapido di Azure fare clic su **Distribuisci in Azure**, immettere la configurazione della crittografia nel pannello **Parametri**, quindi fare clic su **OK**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le note legali e il contratto, quindi fare clic su **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione che usano un ID client di Azure AD:

| Parametro | Descrizione |
| --- | --- |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. Questo parametro è facoltativo se si seleziona **nokek** dall'elenco a discesa UseExistingKek. Se si seleziona **kek** dall'elenco a discesa UseExistingKek, è necessario immettere il valore _keyEncryptionKeyURL_. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono _OS_, _Data_ e _All_. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia dei dischi nella stessa VM. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |

> [!NOTE]
> _KeyEncryptionKeyURL_ è un parametro facoltativo. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per l'ulteriore protezione della chiave DEK (segreto di crittografia di BitLocker) nell'insieme di credenziali delle chiavi.

#### <a name="using-powershell-cmdlets"></a>Usare i cmdlet PowerShell
Per informazioni sull'abilitazione della crittografia con Crittografia dischi di Azure tramite cmdlet di PowerShell, vedere i post di blog [Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) (Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 1) ed [Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) (Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 2).

#### <a name="using-cli-commands"></a>Uso dei comandi dell'interfaccia della riga di comando
Per abilitare la crittografia in macchine virtuali IaaS Windows esistenti o in esecuzione in Azure usando i comandi dell'interfaccia della riga di comando, seguire questa procedura:

1. Per configurare i criteri di accesso nell'insieme di credenziali delle chiavi:
   * Impostare il flag **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Configurare le autorizzazioni per l'applicazione Azure AD per scrivere segreti nell'insieme di credenziali delle chiavi:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Per abilitare la crittografia in una VM esistente o in esecuzione:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Per ottenere lo stato della crittografia:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Per abilitare la crittografia in una nuova macchina virtuale dal disco rigido virtuale crittografato, usare i parametri seguenti con il comando `azure vm create`:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Abilitare la crittografia in una VM IaaS Linux esistente o in esecuzione in Azure
È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Linux esistenti o in esecuzione usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Nel modello di avvio rapido di Azure fare clic su **Distribuisci in Azure**, immettere la configurazione della crittografia nel pannello **Parametri**, quindi fare clic su **OK**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le note legali e il contratto, quindi fare clic su **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione che usano un ID client di Azure AD:

| Parametro | Descrizione |
| --- | --- |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. Questo parametro è facoltativo se si seleziona **nokek** dall'elenco a discesa UseExistingKek. Se si seleziona **kek** dall'elenco a discesa UseExistingKek, è necessario immettere il valore _keyEncryptionKeyURL_. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori supportati validi sono _OS_ o _All_ (per RHEL 7.2, CentOS 7.2 e Ubuntu 16.04) e _Data_ (per tutte le altre distribuzioni). |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia dei dischi nella stessa VM. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |
| passPhrase | Immettere una passphrase complessa come chiave di crittografia dei dati. |

> [!NOTE]
> _KeyEncryptionKeyURL_ è un parametro facoltativo. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per la protezione aggiuntiva della chiave DEK (segreto passphrase) nell'insieme di credenziali delle chiavi.

#### <a name="cli-commands"></a>Comandi dell'interfaccia della riga di comando
È possibile abilitare la crittografia dei dischi nel disco rigido virtuale crittografato installando e usando il [comando dell'interfaccia della riga di comando](../cli-install-nodejs.md). Per abilitare la crittografia in macchine virtuali IaaS Linux esistenti o in esecuzione in Azure usando i comandi dell'interfaccia della riga di comando, seguire questa procedura:

1. Configurare i criteri di accesso nell'insieme di credenziali delle chiavi:

 * Impostare il flag **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Configurare le autorizzazioni per l'applicazione Azure AD per scrivere segreti nell'insieme di credenziali delle chiavi:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Per abilitare la crittografia in una VM esistente o in esecuzione:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Ottenere lo stato della crittografia:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Per abilitare la crittografia in una nuova macchina virtuale dal disco rigido virtuale crittografato, usare i parametri seguenti con il comando `azure vm create`:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Ottenere lo stato della crittografia di una VM IaaS crittografata
È possibile ottenere lo stato della crittografia usando Azure Resource Manager, i [cmdlet di PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti illustrano come usare il portale di Azure classico e i comandi dell'interfaccia della riga di comando per ottenere lo stato della crittografia.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Ottenere lo stato della crittografia di una VM Windows crittografata usando Azure Resource Manager
È possibile ottenere lo stato di crittografia della macchina virtuale IaaS da Azure Resource Manager seguendo questa procedura:

1. Accedere al [portale di Azure classico](https://portal.azure.com/), quindi fare clic su **Macchine virtuali** nel riquadro a sinistra per visualizzare un riepilogo delle macchine virtuali nella sottoscrizione. È possibile filtrare la visualizzazione Macchine virtuali selezionando il nome della sottoscrizione dall'elenco a discesa **Sottoscrizione**.

2. Nella pagina superiore della pagina **Macchine virtuali** fare clic su **Colonne**.

3. Nel pannello  **Scegli colonna** selezionare **Crittografia del disco**, quindi fare clic su **Aggiorna**. Verrà visualizzata la colonna relativa alla crittografia dei dischi, che mostra lo stato della crittografia, ovvero _Abilitato_ o _Non abilitato_, per ogni macchina virtuale, come illustrato nella figura seguente:

 ![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Ottenere lo stato della crittografia di una VM Iaas crittografata (Windows/Linux) usando il cmdlet di PowerShell per la crittografia dei dischi
È possibile ottenere lo stato della crittografia della macchina virtuale IaaS cmdlet `Get-AzureRmVMDiskEncryptionStatus` di PowerShell per la crittografia dei dischi. Per ottenere le impostazioni di crittografia per la macchina virtuale, immettere quanto segue:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

È possibile esaminare l'output di _Get-AzureRmVMDiskEncryptionStatus_ per ottenere gli URL della chiave di crittografia.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

I valori delle impostazioni OSVolumeEncrypted e DataVolumesEncrypted sono impostati su _Encrypted_, che mostra che entrambi i volumi sono crittografati tramite Crittografia dischi di Azure. Per informazioni sull'abilitazione della crittografia con Crittografia dischi di Azure tramite cmdlet di PowerShell, vedere i post di blog [Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) (Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 1) ed [Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) (Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 2).

> [!NOTE]
> Nelle macchine virtuali Linux sono necessari tre o quattro minuti per la visualizzazione dello stato della crittografia da parte del cmdlet `Get-AzureRmVMDiskEncryptionStatus`.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Ottenere lo stato della crittografia della VM IaaS usando il comando dell'interfaccia della riga di comando per la crittografia dei dischi
È possibile ottenere lo stato della crittografia della macchina virtuale IaaS usando il comando `azure vm show-disk-encryption-status` dell'interfaccia della riga di comando per la crittografia dei dischi. Per ottenere le impostazioni delle crittografia per la VM, nella sessione dell'interfaccia della riga di comando digitare:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Disabilitare la crittografia nelle VM IaaS Windows in esecuzione
È possibile disabilitare la crittografia in una VM IaaS Windows o Linux in esecuzione tramite il modello di Resource Manager per Crittografia dischi di Azure o i cmdlet di PowerShell e specificare la configurazione della decrittografia.

##### <a name="windows-vm"></a>Macchina virtuale Windows
Il passaggio per disabilitare la crittografia agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione. Non è possibile disabilitare il volume del sistema operativo e lasciare il volume dati crittografato. Dopo aver completato il passaggio per disabilitare la crittografia, il modello di distribuzione classica di Azure aggiorna il modello di servizi della VM e la VM IaaS Windows viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato. La decrittografia non elimina l'insieme di credenziali delle chiavi e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per Windows o Passphrase per Linux.

##### <a name="linux-vm"></a>VM Linux
Il passaggio per disabilitare la crittografia agisce sul volume dati nella VM IaaS Linux in esecuzione.

> [!NOTE]
> La disabilitazione della crittografia del disco del sistema operativo non è consentita nelle macchine virtuali Linux.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Disabilitare la crittografia in una macchina virtuale IaaS esistente o in esecuzione
È possibile disabilitare la crittografia dei dischi in macchine virtuali IaaS Windows usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. Nel modello di avvio rapido di Azure fare clic su **Distribuisci in Azure**, immettere la configurazione della decrittografia nel pannello **Parametri**, quindi fare clic su **OK**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le note legali e il contratto, quindi fare clic su **Crea** per abilitare la crittografia in una nuova macchina virtuale IaaS.

Per le macchine virtuali Linux è possibile disabilitare la crittografia usando il modello [Disable encryption on a running Linux VM](https://aka.ms/decrypt-linuxvm) (Disabilitare la crittografia in una VM Linux in esecuzione).

La tabella seguente elenca i parametri del modello di Resource Manager per la disabilitazione della crittografia in una VM IaaS in esecuzione:

| Parametro | Descrizione |
| --- | --- |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia.
| volumeType | Tipo del volume in cui viene eseguita l'operazione di decrittografia. I valori validi sono _OS_, _Data_ e _All_. Non è possibile disabilitare la crittografia sul volume del sistema operativo o di avvio di una macchina virtuale IaaS Windows in esecuzione senza disabilitare la crittografia sul volume _Data_. Si noti anche che la disabilitazione della crittografia del disco del sistema operativo non è consentita nelle macchine virtuali Linux. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di decrittografia dei dischi nella stessa VM. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Disabilitare la crittografia in una macchina virtuale IaaS esistente o in esecuzione
Per disabilitare la crittografia in una VM IaaS esistente o in esecuzione usando il cmdlet di PowerShell, vedere [`Disable-AzureRmVMDiskEncryption`](https://msdn.microsoft.com/library/azure/mt715776.aspx). Questo cmdlet supporta sia le VM Windows che le VM Linux. Per disabilitare la crittografia, installa un'estensione nella macchina virtuale. Se il parametro _Name_ non viene specificato, viene creata un'estensione con nome predefinito _AzureDiskEncryption for Windows VMs_.

Nelle VM Linux viene usata l'estensione AzureDiskEncryptionForLinux.

> [!NOTE]
> Questo cmdlet riavvia la macchina virtuale.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Abilitare la crittografia su macchina virtuale IaaS pre-crittografata con disco gestito Azure
Per creare una macchina virtuale crittografata da un disco rigido virtuale pre-crittografato usare il modello ARM di disco gestito Azure, disponibile in   
[Create a new encrypted managed disk from a pre-encrypted VHD/storage blob] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Abilitare la crittografia su una nuova macchina virtuale IaaS Linux con disco gestito Azure
Per creare una nuova macchina virtuale IaaS Linux crittografata usare il modello ARM di disco gestito Azure, disponibile in   
[Deployment of RHEL 7.2 with full disk encryption] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Abilitare la crittografia su una nuova macchina virtuale IaaS Windows con disco gestito Azure
 Per creare una nuova macchina virtuale IaaS Linux crittografata usare il modello ARM di disco gestito Azure, disponibile in   
 [Create a new encrypted Windows IaaS Managed Disk VM from gallery image] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >È necessario usare il parametro -skipVmBackup quando si usa il cmdlet PS di Crittografia dischi di Azure Set-AzureRmVMDiskEncryptionExtension o il comando CLI per abilitare la crittografia sulla macchina virtuale con disco gestito Azure Managed Disk VM.
  >
  >È consigliabile eseguire il backup dell'istanza di macchina virtuale in esecuzione prima di abilitare la crittografia usando il cmdlet PS Set-AzureRmVMDiskEncryptionExtension sulla macchina virtuale con disco gestito Linux.

### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Aggiornare le impostazioni di crittografia di una macchina virtuale non Premium crittografata esistente
  Usare le interfacce supportate di Crittografia dischi di Azure esistenti per le macchine virtuali in esecuzione [cmdlet PS, CLI o modelli ARM] per aggiornare impostazioni di crittografia quali ID/segreto del client AAD, chiave di crittografia della chiave [KEK], chiave di crittografia BitLocker per macchina virtuale Windows o Passphrase per macchina virtuale Linux e così via. L'impostazione della crittografia di aggiornamento è supportata solo per le macchine virtuali non dotate di archiviazione Premium. NON è supportata per le macchine virtuali dotate di archiviazione Premium.

## <a name="appendix"></a>Appendice
### <a name="connect-to-your-subscription"></a>Eseguire la connessione alla sottoscrizione
Prima di continuare, vedere la sezione *Prerequisiti* di questo articolo. Dopo essersi assicurati che siano stati rispettati tutti i prerequisiti, connettersi alla sottoscrizione seguendo questa procedura:

1. Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

    `Login-AzureRmAccount`

2. Se sono disponibili più sottoscrizioni e se ne vuole specificare una da usare, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:

    `Get-AzureRmSubscription`

3. Per specificare la sottoscrizione che si vuole usare, digitare:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Per verificare che la sottoscrizione configurata sia corretta, digitare:

    `Get-AzureRmSubscription`

5. Per verificare che i cmdlet di Crittografia dischi di Azure siano installati, digitare:

    `Get-command *diskencryption*`

6. L'output seguente conferma l'installazione di PowerShell per Crittografia dischi di Azure:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparare un disco rigido virtuale Windows pre-crittografato
Le sezioni seguenti sono necessarie per preparare un disco rigido virtuale Windows pre-crittografato per la distribuzione come disco rigido virtuale crittografato in Azure IaaS. Usare le informazioni per preparare e avviare una nuova macchina virtuale Windows VM (disco rigido virtuale) in Azure Site Recovery o Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aggiornare i criteri di gruppo per consentire la protezione non TPM del sistema operativo
Configurare l'impostazione **Crittografia unità BitLocker** di Criteri di gruppo per BitLocker, disponibile in **Criteri del computer locale** > **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows**. Cambiare questa impostazione in **Unità del sistema operativo** > **Richiedi autenticazione aggiuntiva all'avvio** > **Consenti BitLocker senza un TPM compatibile**, come illustrato nella figura seguente:

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Installare i componenti della funzionalità BitLocker
Per Windows Server 2012 e versioni successive, usare il comando seguente:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Per Windows Server 2008 R2, usare il comando seguente:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparare il volume del sistema operativo per BitLocker tramite `bdehdcfg`
Per comprimere la partizione del sistema operativo e preparare il computer per BitLocker, eseguire il comando seguente:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteggere il volume del sistema operativo usando BitLocker
Usare il comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) per abilitare la crittografia sul volume di avvio usando una protezione con chiave esterna. Salvare anche la chiave esterna (file con estensione bek) nell'unità o nel volume esterno. La crittografia viene abilitata nel volume di sistema/di avvio al riavvio successivo.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Preparare la macchina virtuale con un disco rigido virtuale dati/di risorse separato per recuperare la chiave esterna usando BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Crittografia di un'unità del sistema operativo in una VM Linux in esecuzione
La crittografia di un'unità del sistema operativo in una VM Linux in esecuzione è supportata nelle distribuzioni seguenti:

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>Prerequisiti per la crittografia del disco del sistema operativo

* La macchina virtuale deve essere creata dall'immagine del Marketplace in Azure Resource Manager.
* VM di Azure con almeno 4 GB di RAM (7 GB consigliati).
* (Per RHEL e CentOS) Disabilitare SELinux. Per disabilitare SELinux, vedere "4.4.2. Disabling SELinux" (4.4.2. Disabilitazione di SELinux) in [SELinux User's and Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) (Manuale dell'utente e dell'amministratore di SELinux) nella VM.
* Dopo la disabilitazione di SELinux, riavviare la VM almeno una volta.

##### <a name="steps"></a>Passi
1. Creare una macchina virtuale usando una delle distribuzioni specificate in precedenza.

 Per CentOS 7.2, la crittografia del disco del sistema operativo è supportata tramite un'immagine speciale. Per usare questa immagine, specificare "7.2n" come SKU durante la creazione della VM:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Configurare la VM in base alle esigenze. Se si intende crittografare tutte le unità (sistema operativo e dati), le unità dati dovranno essere specificate e montabili da /etc/fstab.

 > [!NOTE]
 > Usare UUID=... per specificare le unità di dati in /etc/fstab anziché specificare il nome del dispositivo a blocchi, ad esempio /dev/sdb1. L'ordine delle unità viene modificato nella macchina virtuale durante la crittografia. Se la VM si basa su un ordine specifico di dispositivi a blocchi, questi dispositivi non potranno essere montati dopo la crittografia.

3. Disconnettersi dalle sessioni SSH.

4. Per crittografare il sistema operativo, specificare volumeType come **All** oppure **OS** quando si [abilita la crittografia](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Tutti i processi dello spazio dell'utente non in esecuzione come servizi `systemd` devono essere terminati con `SIGKILL`. Riavviare la macchina virtuale. Quando si abilita la crittografia del disco del sistema operativo in una macchina virtuale in esecuzione, pianificare i tempi di inattività della VM.

5. Monitorare periodicamente lo stato della crittografia tramite le istruzioni indicate nella [sezione successiva](#monitoring-os-encryption-progress).

6. Quando Get-AzureRmVmDiskEncryptionStatus indica "VMRestartPending," riavviare la macchina virtuale accedendo alla VM o usando il portale, PowerShell oppure l'interfaccia della riga di comando.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Prima di riavviare, è consigliabile salvare i dati di [diagnostica di avvio](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) della macchina virtuale.

#### <a name="monitoring-os-encryption-progress"></a>Monitoraggio dello stato della crittografia del sistema operativo
Esistono tre modi per monitorare lo stato della crittografia del sistema operativo:

* Usare il cmdlet `Get-AzureRmVmDiskEncryptionStatus` ed esaminare il campo ProgressMessage:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Quando la VM raggiunge lo stato "OS disk encryption started", sono necessari circa 40 o 50 minuti in una VM con archiviazione Premium.

 A causa dell'[errore #388](https://github.com/Azure/WALinuxAgent/issues/388) in WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted`vengono visualizzati come `Unknown` in alcune distribuzioni. Con WALinuxAgent 2.1.5 e versioni successive questo errore viene risolto automaticamente. Se viene visualizzato `Unknown` nell'output, è possibile verificare lo stato della crittografia del disco usando Esplora risorse di Azure.

 Passare a [Esplora risorse di Azure](https://resources.azure.com/), quindi espandere questa gerarchia nel pannello di selezione a sinistra:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 In InstanceView scorrere verso il basso per visualizzare lo stato della crittografia delle unità.

 ![Visualizzazione dell'istanza della VM](./media/azure-security-disk-encryption/vm-instanceview.png)

* Esaminare la [diagnostica di avvio](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). I messaggi provenienti dall'estensione ADE hanno il prefisso `[AzureDiskEncryption]`.

* Accedere alla VM tramite SSH e ottenere il log di estensione da:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 È consigliabile non accedere alla VM mentre è in corso la crittografia del sistema operativo. Copiare i log solo in caso di errore degli altri due metodi.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Preparare un disco rigido virtuale Linux pre-crittografato
##### <a name="ubuntu-16"></a>Ubuntu 16
Configurare la crittografia durante l'installazione della distribuzione seguendo questa procedura:

1. Selezionare **Configure encrypted volumes** (Configura volumi crittografati) durante il partizionamento dei dischi.

 ![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Creare un'unità di avvio separata che non deve essere crittografata. Crittografare l'unità radice.

 ![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Specificare una passphrase. Si tratta della passphrase che viene caricata nell'insieme di credenziali delle chiavi.

 ![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Terminare il partizionamento.

 ![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando si avvia la macchina virtuale e viene richiesta una passphrase, usare la passphrase specificata nel passaggio 3.

 ![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Preparare la macchina virtuale per il caricamento in Azure seguendo [queste istruzioni](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

Configurare la crittografia per l'uso in Azure seguendo queste istruzioni:

1. Creare un file in /usr/local/sbin/azure_crypt_key.sh, con il contenuto dello script seguente. Prestare attenzione a KeyFileName, perché è il nome file della passphrase usato da Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Modificare la configurazione di crittografia in */etc/crypttab*. L'aspetto dovrebbe risultare simile al seguente:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Se si modifica *azure_crypt_key.sh* in Windows e questo valore è stato copiato in Linux, eseguire `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Aggiungere autorizzazioni di esecuzione allo script:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Modificare */etc/initramfs-tools/modules* accodando le righe:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Eseguire `update-initramfs -u -k all` per aggiornare initramfs e rendere operativo il `keyscript`.

7. È ora possibile effettuare il deprovisioning della VM.

 ![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continuare con il passaggio successivo e [caricare il disco rigido virtuale](#upload-encrypted-vhd-to-an-azure-storage-account) in Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Per configurare la crittografia durante l'installazione della distribuzione, seguire questa procedura:
1. Durante il partizionamento dei dischi, selezionare **Encrypt Volume Group** (Crittografa gruppo di volumi), quindi immettere una password. Questa è la password che verrà caricata nell'insieme di credenziali delle chiavi.

 ![Configurazione di openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Avviare la VM usando la password.

 ![Configurazione di openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Preparare la macchina virtuale per il caricamento in Azure seguendo le istruzioni disponibili in [Preparare una macchina virtuale SLES oppure openSUSE per Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

Per configurare la crittografia per l'uso in Azure, seguire questa procedura:
1. Modificare il file /etc/dracut.conf e aggiungere la riga seguente:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Impostare come commento queste righe verso la fine del file /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Aggiungere la riga seguente all'inizio del file /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
 ```
    DRACUT_SYSTEMD=0
 ```
Modificare quindi tutte le occorrenze di:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
in:
```
    if [ 1 ]; then
```
4. Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere questo codice dopo "# Open LUKS device":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Eseguire `/usr/sbin/dracut -f -v` per aggiornare initrd.

6. È ora possibile effettuare il deprovisioning della macchina virtuale e [caricare il disco rigido virtuale](#upload-encrypted-vhd-to-an-azure-storage-account) in Azure.

##### <a name="centos-7"></a>CentOS 7
Per configurare la crittografia durante l'installazione della distribuzione, seguire questa procedura:
1. Selezionare **Encrypt my data** (Crittografa dati personali) durante il partizionamento dei dischi.

 ![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Assicurarsi **Encrypt** (Crittografa) sia selezionato per la partizione radice.

 ![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Specificare una passphrase. Questa è la passphrase che verrà caricata nell'insieme di credenziali delle chiavi.

 ![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Quando si avvia la macchina virtuale e viene richiesta una passphrase, usare la passphrase specificata nel passaggio 3.

 ![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Preparare la macchina virtuale per il caricamento in Azure usando le istruzioni relative a "CentOS 7.0+" disponibili in [Preparare una macchina virtuale basata su CentOS per Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

6. È ora possibile effettuare il deprovisioning della macchina virtuale e [caricare il disco rigido virtuale](#upload-encrypted-vhd-to-an-azure-storage-account) in Azure.

Per configurare la crittografia per l'uso in Azure, seguire questa procedura:

1. Modificare il file /etc/dracut.conf e aggiungere la riga seguente:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Impostare come commento queste righe verso la fine del file /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Aggiungere la riga seguente all'inizio del file /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
```
    DRACUT_SYSTEMD=0
```
Modificare quindi tutte le occorrenze di:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere questo codice dopo "# Open LUKS device":
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Eseguire "/usr/sbin/dracut -f -v" per aggiornare initrd.

![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Caricare il VHD crittografato in un account di archiviazione di Azure
Dopo aver abilitato la crittografia BitLocker o la crittografia DM-Crypt, il disco rigido virtuale crittografato locale dovrà essere caricato nell'account di archiviazione.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Caricare il segreto di crittografia del disco per la VM pre-crittografata nell'insieme di credenziali delle chiavi
Il segreto di crittografia del disco ottenuto in precedenza deve essere caricato come segreto nell'insieme di credenziali delle chiavi. L'insieme di credenziali delle chiavi deve avere la crittografia dei dischi e le autorizzazioni abilitate per il client di Azure AD.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave
Per configurare il segreto nell'insieme di credenziali delle chiavi, usare [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx). Se si ha una macchina virtuale Windows, il file con estensione bek viene codificato come stringa base64, quindi viene caricato nell'insieme di credenziali delle chiavi usando il cmdlet `Set-AzureKeyVaultSecret`. Per Linux la passphrase viene codificata come stringa Base 64 e quindi caricata nell'insieme di credenziali delle chiavi. Assicurarsi anche che i tag seguenti siano impostati quando si crea il segreto nell'insieme di credenziali delle chiavi.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Usare `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo senza usare una chiave di crittografia della chiave](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave
Prima di caricare il segreto nell'insieme di credenziali delle chiavi, è possibile crittografarlo usando una chiave di crittografia della chiave. Usare l'[API](https://msdn.microsoft.com/library/azure/dn878066.aspx) WRAP per crittografare prima di tutto il segreto con la chiave di crittografia della chiave. L'output di questa operazione WRAP si basa su una stringa con codifica Base 64 dell'URL che può essere quindi caricata come segreto con il cmdlet [`Set-AzureKeyVaultSecret`](https://msdn.microsoft.com/library/dn868050.aspx).

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Usare `$KeyEncryptionKey` e `$secretUrl` nel passaggio successivo per [collegare il disco del sistema operativo usando una chiave di crittografia della chiave](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Specificare un URL del segreto quando si collega un disco del sistema operativo
#### <a name="without-using-a-kek"></a>Senza l'uso di una chiave di crittografia della chiave (KEK)
Durante il collegamento del disco del sistema operativo è necessario passare `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Uso di una chiave di crittografia della chiave (KEK)
Quando si collega il disco del sistema operativo, vedere `$KeyEncryptionKey` e `$secretUrl`. L'URL è stato generato nella sezione "Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Scaricare questa guida
È possibile scaricare in questa guida dalla [Raccolta TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## <a name="for-more-information"></a>Per altre informazioni
[Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Esplorare Crittografia dischi di Azure con Azure PowerShell - Parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

