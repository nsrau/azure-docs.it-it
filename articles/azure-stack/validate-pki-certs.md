---
title: La convalida dei certificati di infrastruttura a chiave pubblica Azure dello Stack per la distribuzione di sistemi Azure Stack integrato | Documenti Microsoft
description: Viene descritto come convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack per i sistemi Azure Stack integrato.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: b38e3cc45d14645611c0cd804f2bfa66047810f0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>La convalida dei certificati di infrastruttura a chiave pubblica di Azure Stack
Lo strumento di controllo di Azure Stack certificato descritto in questo articolo viene fornito dall'OEM incluso con il file deploymentdata.json per convalidare che il [generati certificati PKI](azure-stack-get-pki-certs.md) adatti per la pre-distribuzione. Con un tempo sufficiente per testare e ottenere i certificati emessi nuovamente se necessario, è necessario convalidare i certificati. 

Lo strumento di controllo di certificato (Certchecker) esegue i controlli seguenti:

- **Leggere PFX**. Verifica la presenza di file PFX valido, la password corretta e genera un avviso se informazioni pubblico non protetto da password. 
- **Algoritmo di firma**. Controlla che l'algoritmo di firma non è SHA1 
- **Chiave privata**. Controlla la chiave privata è presente e viene esportata con l'attributo del computer locale. 
- **Catena di certificazione**. Controlla la catena di certificati è di per i certificati autofirmati, tra cui pulsante. 
- **I nomi DNS**. Controlla la SAN contiene i nomi DNS pertinenti per ogni endpoint o un supporto con caratteri jolly non è presente. 
- **Utilizzo chiave**. Verifica utilizzo chiavi avanzato contiene firma digitale e la crittografia della chiave e l'utilizzo chiavi avanzato include autenticazione Server e Client. 
- **Dimensione chiave**. Controlla che le dimensioni di chiave è 2048 o superiore 
- **Concatenare ordine**. Controlla l'ordine dei certificati effettua la catena è corretto. 
- **Altri certificati**. Verificare che altri certificati non sono state incluse in PFX diverso dal certificato foglia pertinenti e la catena. 
- **Nessun profilo**. Verifica di che un nuovo utente può caricare i dati PFX senza un profilo utente caricato, simulando il comportamento di account gMSA durante la manutenzione di certificato.   

> [!IMPORTANT]
> Il file PFX del certificato PKI e la password devono essere considerate come informazioni riservate.

## <a name="prerequisites"></a>Prerequisiti
Il sistema deve soddisfare i prerequisiti seguenti prima di convalidare i certificati PKI per la distribuzione di Azure Stack:
- CertChecker (in PartnerToolKit in \utils\certchecker)
- I certificati SSL esportato seguendo il [istruzioni relative alla preparazione](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 o Windows Server 2016

## <a name="perform-certificate-validation"></a>Eseguire la convalida dei certificati
Utilizzare questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack: 

1. Estrarre il contenuto del <partnerToolkit>\utils\certchecker in una nuova directory, ad esempio, **c:\certchecker**.

2. Aprire PowerShell come amministratore e passare alla directory della cartella certchecker:

  ```powershell
  cd c:\certchecker
  ```
 
3. Creare una struttura di directory per i certificati eseguendo i comandi di PowerShell seguenti:

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Se il provider di identità per la distribuzione dello Stack di Azure AD Azure, rimuovere il **ADFS** e **grafico** directory. 

4. Sul posto dei certificati nelle directory appropriate creato nel passaggio precedente, ad esempio: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - E così via... 

5. Copia **deploymentdata.json** nel **c:\certchecker** directory.

6. Nella finestra di PowerShell eseguire i comandi seguenti: 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. L'output deve contenere OK per tutti i certificati e tutti gli attributi selezionati: 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Problemi noti 
**Sintomo**: Certchecker viene chiusa in modo anomalo e viene visualizzato l'errore seguente: 
> Operazione non riuscita

> Dettagli: Il comando non può essere eseguito a causa dell'errore: il nome della directory non è valido. 

**Causa**: è in esecuzione certchecker.ps1 da una cartella restrittiva, ad esempio, c:\temp o % temp % 

**Risoluzione**: sposta lo strumento certchecker nuova directory, ad esempio, C:\CertChecker 


**Sintomo**: Certchecker viene visualizzato un avviso sull'utilizzo di Pre-1803 (come nell'esempio sopra indicato nel passaggio 7):

> [!WARNING]
> Struttura del certificato di pre-1803. La cartella struttura 1803 dello Stack di Azure e versioni successive sia: ACSTable ACSBlob, ACSQueue, anziché la cartella ACS. Per ulteriori informazioni, vedere la documentazione sulla distribuzione.

**Causa**: CertChecker ha rilevato l'utilizzo di una singola cartella di ACS, il valore è corretto per le distribuzioni prima 1803. Per la versione di Azure Stack 1803 e sopra le distribuzioni, la struttura di cartelle diventa ACSTable, ACSQueue, ACSBlob. Certchecker è già essere aggiornata per supportare questa funzionalità.

**Risoluzione**: se la distribuzione 1802, è necessaria alcuna azione. Se la distribuzione 1803 e versioni successive, sostituire ACS con ACSTable, ACSQueue, ACSBlob e copiare i certificati di ACS in tali cartelle.

**Sintomo**: test vengono ignorati

**Causa**: CertChecker Ignora determinati test se non viene soddisfatta una dipendenza:
- Altri certificati vengono ignorati se la catena di certificati non riesce.
- Nessun profilo viene ignorato se:
  - È un criterio di sicurezza limitando la possibilità di creare un utente temporaneo ed eseguire powershell come tale utente.
  - Controllo chiave privata non riesce.

**Risoluzione**: seguire le linee guida degli strumenti nella sezione dei dettagli in set di ogni certificato di test.


## <a name="prepare-deployment-script-certificates"></a>Preparare i certificati di script di distribuzione 
Come passaggio finale, è necessario che tutti i certificati che è stata preparata si trovino nelle directory appropriate nell'host di distribuzione. Nell'host di distribuzione, creare una cartella denominata. Certificati * * e inserire il certificato esportato i file nelle sottocartelle corrispondente specificate nella [certificati obbligatori](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) sezione:

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> I certificati contrassegnati con un asterisco * sono necessari solo quando ADFS viene utilizzato come archivio di identità.


## <a name="next-steps"></a>Passaggi successivi
[Integrazione delle identità datacenter](azure-stack-integrate-identity.md)