---
title: Risolvere i problemi di certificato per Azure Stack | Microsoft Docs
description: Usare il controllo di conformità di Azure Stack per esaminare e correggere i problemi di certificato.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 5e96c731496d79ca081091e2059a35545f963bd6
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078637"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Risoluzione dei problemi più comuni per i certificati di infrastruttura a chiave pubblica di Azure Stack
Le informazioni contenute in questo articolo consente di comprendere e risolvere i problemi comuni per i certificati di infrastruttura a chiave pubblica di Azure Stack. È possibile individuare i problemi quando si usa lo strumento di controllo di conformità di Azure Stack per [convalida dei certificati di infrastruttura a chiave pubblica di Azure Stack](azure-stack-validate-pki-certs.md). Lo strumento di verifica per assicurarsi che i certificati soddisfino i requisiti di infrastruttura a chiave pubblica di una distribuzione di Azure Stack e rotazione segreto di Azure Stack e registra i risultati un [report.json file](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Crittografia PFX
**Errore** -PFX non è crittografia TripleDES, SHA1.   
**Correzione** -i file di esportazione file PFX con **TripleDES, SHA1** crittografia. Questo è il valore predefinito per tutti i client di 10 di Windows durante l'esportazione dal certificato dello snap-in o usando Export-PFXCertificate. 

## <a name="read-pfx"></a>Leggere file PFX
**Avviso** -Password protegge solo le informazioni private del certificato.  
**Correzione** -si consiglia esportare i file PFX con l'impostazione facoltativa per **Abilita privacy del certificato**.  

**Errore** -PFX file valido.  
**Correzione** -esportare nuovamente il certificato utilizzando la procedura descritta in [certificati di preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo di firma
**Errore** -algoritmo di firma è SHA1.    
**Monitoraggio e aggiornamento** -usare la procedura per rigenerare la richiesta di firma del certificato (CSR) con l'algoritmo di firma di SHA256 generazione di richiesta di firma dei certificati di Azure Stack. Quindi inviare nuovamente la richiesta CSR all'autorità di certificazione per eseguire nuovamente il certificato.

## <a name="private-key"></a>Chiave privata
**Errore** -la chiave privata mancante o non contiene l'attributo macchina locale.  
**Monitoraggio e aggiornamento** - dal computer che ha generato il file CSR, esportare nuovamente il certificato seguendo i passaggi contenuti certificati preparare Azure Stack PKI per la distribuzione. Tali passaggi includono l'esportazione dall'archivio certificati computer locale.

## <a name="certificate-chain"></a>Catena di certificati
**Errore** -catena di certificati non è stata completata.  
**Monitoraggio e aggiornamento** -certificati devono contenere una catena di certificati completa.  Esportare nuovamente il certificato utilizzando la procedura descritta in [certificati preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md) e selezionare l'opzione **se possibile, Includi tutti i certificati nel percorso di certificazione.**

## <a name="dns-names"></a>Nomi DNS
**Errore** -DNSNameList nel certificato non contiene il nome dell'endpoint servizio di Azure Stack o una corrispondenza con caratteri jolly validi.  Corrispondenze con caratteri jolly sono valide solo per lo spazio dei nomi di più a sinistra del nome DNS. Ad esempio, _*. region.domain.com_ è valida solo per *portal.region.domain.com*, non _*. table.region.domain.com_.  
**Monitoraggio e aggiornamento** -usare i passaggi nei certificati di Azure Stack per rigenerare il file CSR con i nomi DNS corretti generazione di richiesta di firma per supportare gli endpoint di Azure Stack. Inviare di nuovo la richiesta CSR a un'autorità di certificazione e quindi seguire i passaggi descritti in [certificati preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md) per esportare il certificato dal computer che ha generato il file CSR.  

## <a name="key-usage"></a>Utilizzo delle chiave
**Errore** - Key Usage manca una firma digitale o manca la crittografia della chiave, orEnhanced utilizzo chiavi avanzato autenticazione Server o Client.  
**Correzione** -utilizzare la procedura descritta in [firma richiedere la generazione di certificati di Azure Stack](azure-stack-get-pki-certs.md) rigenerare il file CSR con gli attributi di utilizzo chiave corretti.  Inviare di nuovo la richiesta CSR all'autorità di certificazione e confermare che un modello di certificato non sovrascrive l'utilizzo di chiavi nella richiesta.

## <a name="key-size"></a>Dimensioni chiave
**Errore** -dimensioni di chiave sono inferiori a 2048    
**Correzione** -utilizzare la procedura descritta in [firma richiedere la generazione di certificati di Azure Stack](azure-stack-get-pki-certs.md) per rigenerare il file CSR con la lunghezza di chiave corretta (2048) e quindi inviare di nuovo la richiesta CSR all'autorità di certificazione.

## <a name="chain-order"></a>Ordine di catena
**Errore** -l'ordine della catena di certificati non è corretto.  
**Correzione** -esportare nuovamente il certificato utilizzando la procedura descritta in [certificati di preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md) e selezionare l'opzione **se possibile, Includi tutti i certificati nel percorso di certificazione.** Assicurarsi che sia selezionato solo il certificato foglia per l'esportazione. 

## <a name="other-certificates"></a>Altri certificati
**Errore** -PFX pacchetto contiene i certificati che non sono il certificato foglia o parte della catena di certificati.  
**Correzione** -esportare nuovamente il certificato utilizzando la procedura descritta in [certificati di preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md), quindi selezionare l'opzione **se possibile, Includi tutti i certificati nel percorso certificazione.** Assicurarsi che sia selezionato solo il certificato foglia per l'esportazione.

## <a name="fix-common-packaging-issues"></a>Risolvere problemi comuni di creazione di pacchetti
Il AzsReadinessChecker possibile importare e quindi esportare un file PFX per risolvere problemi comuni di creazione dei pacchetti, tra cui: 
 - *Crittografia PFX* non TripleDES, SHA1
 - *Chiave privata* manca l'attributo macchina locale.
 - *Catena di certificati* è incompleta o errata. (Computer locale deve contenere la catena di certificati se il pacchetto PFX non esiste.) 
 - *Altri certificati*.
Tuttavia, non consente del AzsReadinessChecker se è necessario generare una nuova, eseguire nuovamente un certificato. 

### <a name="prerequisites"></a>Prerequisiti
I prerequisiti seguenti devono essere presenti nel computer in cui viene eseguito lo strumento: 
 - Windows 10 o Windows Server 2016, con connettività internet.
 - PowerShell 5.1 o versione successiva. Per controllare la versione, eseguire il comando PowerShell seguente e quindi esaminare i *principali* versione e *secondaria* versioni.

   > `$PSVersionTable.PSVersion`
 - Configurare [PowerShell per Azure Stack](azure-stack-powershell-install.md). 
 - Scaricare la versione più recente di [verifica di conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

### <a name="import-and-export-an-existing-pfx-file"></a>Importare ed esportare un File PFX esistente
1. In un computer che soddisfi i prerequisiti, aprire un prompt amministrativo di PowerShell e quindi eseguire il comando seguente per installare il AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. Al prompt di PowerShell, eseguire il comando seguente per impostare la password PFX. Sostituire *PFXpassword* con la password effettiva. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. Al prompt di PowerShell, eseguire il comando seguente per esportare un nuovo file PFX.
   - Per la *- PfxPath*, specificare il percorso del file PFX si sta lavorando.  Nell'esempio seguente, il percorso è *.\certificates\ssl.pfx*.
   - Per la *- ExportPFXPath*, specificare il percorso e nome del file PFX per l'esportazione.  Nell'esempio seguente, il percorso è *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Dopo aver completato lo strumento, esaminare l'output per l'esito positivo: ![risultati](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sulla sicurezza di Azure Stack](azure-stack-rotate-secrets.md)
