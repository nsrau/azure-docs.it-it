---
title: Correggere i problemi di certificato per lo Stack di Azure | Documenti Microsoft
description: Utilizzare il controllo di conformità dello Stack di Azure per esaminare e correggere i problemi di certificato.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Correggere i problemi comuni per i certificati di infrastruttura a chiave pubblica di Azure Stack
Le informazioni contenute in questo articolo consente di comprendere e risolvere i problemi comuni per i certificati di infrastruttura a chiave pubblica di Azure dello Stack. È possibile individuare problemi durante l'uso dello strumento di controllo di conformità dello Stack di Azure per [convalidare i certificati di infrastruttura a chiave pubblica di Azure Stack](azure-stack-validate-pki-certs.md). Lo strumento controlla per verificare che i certificati soddisfano i requisiti di infrastruttura a chiave pubblica di una distribuzione dello Stack di Azure e Azure Stack segreto rotazione e registra i risultati un [report.json file](azure-stack-validation-report.md).  

## <a name="read-pfx"></a>Leggere PFX
**Avviso** -Password consente di proteggere solo le informazioni private nel certificato.  
**Correzione** -è consigliabile si esportano il file PFX con l'impostazione facoltativa per **Abilita privacy del certificato**.  

**Errore** -PFX file valido.  
**Correzione** -esportare nuovamente il certificato utilizzando la procedura descritta in [certificati preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo di firma
**Errore** -algoritmo di firma è SHA1.    
**Monitoraggio e aggiornamento** -usare i passaggi nei certificati di Azure Stack generazione per rigenerare la richiesta di firma certificato (CSR) con l'algoritmo di firma di SHA256 di una richiesta di firma. Quindi eseguire nuovamente la richiesta CSR all'autorità di certificazione per eseguire nuovamente il certificato.

## <a name="private-key"></a>Chiave privata
**Errore** -la chiave privata è manca o non contiene l'attributo macchina locale.  
**Monitoraggio e aggiornamento** - dal computer che ha generato la richiesta CSR, esportare nuovamente il certificato mediante i passaggi dei certificati PKI di Stack Azure preparare per la distribuzione. Tali passaggi includono esportazione dall'archivio certificati computer locale.

## <a name="certificate-chain"></a>Catena di certificati
**Errore** -catena di certificati non è stata completata.  
**Monitoraggio e aggiornamento** -certificati devono contenere un'intera catena di certificati.  Esportare nuovamente il certificato utilizzando la procedura descritta in [certificati preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md) e selezionare l'opzione **Includi tutti i certificati nel percorso di certificazione, se possibile.**

## <a name="dns-names"></a>Nomi DNS
**Errore** -DNSNameList nel certificato non contiene il nome dell'endpoint del servizio Azure Stack o una corrispondenza con caratteri jolly validi.  Corrispondenze con caratteri jolly sono valide solo per lo spazio dei nomi a sinistra del nome DNS. Ad esempio _*. region.domain.com_ è valido solo per *portal.region.domain.com*, non _*. table.region.domain.com_.  
**Monitoraggio e aggiornamento** -usare i passaggi nei certificati di Azure Stack firma generazione di una richiesta per rigenerare la richiesta CSR con i nomi corretti del DNS per supportare gli endpoint di Azure Stack. Inviare di nuovo la richiesta CSR a un'autorità di certificazione e quindi seguire i passaggi descritti in [certificati preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md) per esportare il certificato dal computer che ha generato la richiesta CSR.  

## <a name="key-usage"></a>Utilizzo delle chiave
**Errore** - Key Usage manca una firma digitale o la crittografia della chiave, orEnhanced Key Usage manca l'autenticazione Server o l'autenticazione Client.  
**Correzione** -utilizzare la procedura descritta in [Stack Azure i certificati firma richiedere la generazione](azure-stack-get-pki-certs.md) rigenerare la richiesta CSR con gli attributi Key Usage corretti.  Inviare di nuovo la richiesta CSR all'autorità di certificazione e confermare che un modello di certificato non andrà a sovrascrivere l'utilizzo di chiavi nella richiesta.

## <a name="key-size"></a>Dimensioni chiave
**Errore** -chiave dimensioni sono inferiori a 2048    
**Correzione** -utilizzare la procedura descritta in [Stack Azure i certificati firma richiedere la generazione](azure-stack-get-pki-certs.md) per rigenerare la richiesta CSR con la lunghezza di chiave corretta (2048) e quindi inviare di nuovo la richiesta CSR all'autorità di certificazione.

## <a name="chain-order"></a>Ordine di concatenamento
**Errore** -l'ordine della catena di certificati non è corretto.  
**Correzione** -esportare nuovamente il certificato utilizzando la procedura descritta in [certificati preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md) e selezionare l'opzione **Includi tutti i certificati nel percorso di certificazione, se possibile.** Verificare che sia selezionato solo il certificato foglia per l'esportazione. 

## <a name="other-certificates"></a>Altri certificati
**Errore** -pacchetto PFX contiene i certificati che non sono il certificato foglia o parte della catena di certificati.  
**Correzione** -esportare nuovamente il certificato utilizzando la procedura descritta in [certificati preparare Azure Stack PKI per la distribuzione](azure-stack-prepare-pki-certs.md), quindi selezionare l'opzione **Includi tutti i certificati nel percorso di certificazione, se possibile.** Verificare che sia selezionato solo il certificato foglia per l'esportazione.

## <a name="fix-common-packaging-issues"></a>Risolvere i problemi più comuni di creazione di pacchetti
Il AzsReadinessChecker possibile importare e quindi esportare un file PFX per risolvere problemi comuni di creazione di pacchetti, tra cui: 
 - *Chiave privata* manca attributo macchina locale.
 - *Catena di certificati* è incompleta o non corretto. (Computer locale deve contenere la catena di certificati se il pacchetto PFX non lo è.) 
 - *Altri certificati*.
Tuttavia, il AzsReadinessChecker non è utile se è necessario generare una richiesta CSR richiedono nuovo e quindi eseguire nuovamente un certificato. 

### <a name="prerequisites"></a>Prerequisiti
I seguenti prerequisiti devono essere presenti nel computer in cui viene eseguito lo strumento: 
 - Windows 10 o Windows Server 2016, con connettività internet.
 - PowerShell 5.1 o versione successiva. Per controllare la versione in uso, eseguire il seguente cmd PowerShell ed esaminare il *principali* versione e *secondaria* versioni.

   > `$PSVersionTable.PSVersion`
 - Configurare [PowerShell per Azure Stack](azure-stack-powershell-install.md). 
 - Scaricare la versione più recente di [Verifica conformità di Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) dello strumento.

### <a name="import-and-export-an-existing-pfx-file"></a>Importare ed esportare un File PFX esistente
1. In un computer che soddisfa i prerequisiti, aprire un prompt dei comandi di PowerShell amministrativi e quindi eseguire il comando seguente per installare il AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. Al prompt di PowerShell, eseguire il comando seguente per impostare la password PFX. Sostituire *PFXpassword* con la password effettiva. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. Al prompt di PowerShell, eseguire il comando seguente per esportare un nuovo file PFX.
   - Per *- PfxPath*, specificare il percorso del file PFX in uso.  Nell'esempio seguente, il percorso è *.\certificates\ssl.pfx*.
   - Per *- ExportPFXPath*, specificare il percorso e nome del file PFX per l'esportazione.  Nell'esempio seguente, il percorso è *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Dopo aver completato lo strumento, esaminare l'output per l'esito positivo: ![risultati](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>Passaggi successivi
[Ulteriori informazioni sulla sicurezza di Azure Stack](azure-stack-rotate-secrets.md)