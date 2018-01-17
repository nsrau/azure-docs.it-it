---
title: Generare i certificati di infrastruttura a chiave pubblica Azure dello Stack per la distribuzione di sistemi Azure Stack integrato | Documenti Microsoft
description: Vengono descritti i sistemi di infrastruttura a chiave pubblica di Azure Stack certificati distribuzione processfor Stack Azure integrato.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e7edbc1c4aa6e3cb1026d493886ef7ca704b9131
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Generare i certificati PKI per la distribuzione di Azure Stack
Ora che si conoscono [requisiti dei certificati PKI](azure-stack-pki-certs.md) per le distribuzioni di Stack di Azure, è necessario ottenere i certificati dall'autorità di certificazione (CA) di propria scelta. 

## <a name="request-certificates-using-an-inf-file"></a>Richiesta di certificati tramite un file INF
Un modo per richiedere certificati da una CA pubblica o una CA interna viene eseguita tramite un file INF. L'utilità certreq.exe predefinite di Windows è possibile utilizzare un file INF specificando dettagli certificato, per generare un file di richiesta, come descritto in questa sezione. 

### <a name="sample-inf-file"></a>File INF di esempio 
Il file INF richiesta di esempio certificato utilizzabile per creare un file di richiesta di certificato non in linea da inviare a un'autorità di certificazione (interno o pubblico). Il file INF copre tutti gli endpoint necessari (inclusi i servizi PaaS facoltativi) in un certificato con caratteri jolly singolo. 

Il file INF di esempio si presuppone che tale area è uguale a **sea** e il valore FQDN esterno è **sea &#46; contoso &#46; com**. Modificare tali valori in base all'ambiente prima di generare un. File INF per la distribuzione. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Generare e inviare una richiesta all'autorità di certificazione
Flusso di lavoro seguente viene descritto come è possibile personalizzare e utilizzare il file INF di esempio generato in precedenza per richiedere un certificato da un'autorità di certificazione:

1. **Modificare e salvare il file INF**. Copia l'esempio fornito e salvarlo in un nuovo file di testo. Sostituire il nome del soggetto e nome FQDN esterno con i valori corrispondenti della distribuzione e salvare il file come un. File INF.
2. **Generare una richiesta utilizzando certreq**. Utilizza un computer Windows, di avviare un prompt dei comandi come amministratore ed eseguire il comando seguente per generare un file di richiesta (req): `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Inviare all'autorità di certificazione**. Inviare il. File REQ generato per l'autorità di certificazione (può essere interna o pubblica).
4. **Importazione. CER**. Restituisce l'autorità di certificazione una. File CER. Con lo stesso computer di Windows da cui è stato generato il file di richiesta, importare il. File CER restituito nell'archivio del computer/personale. 
5. **Esportare e copiare. PFX per le cartelle di distribuzione**. Esportare il certificato (con la chiave privata) come un. PFX file e copiare il. Il file PFX per le cartelle di distribuzione descritti in [requisiti di infrastruttura a chiave pubblica di Azure Stack distribuzione](azure-stack-pki-certs.md).

