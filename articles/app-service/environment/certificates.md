---
title: Associazioni di certificati
description: Illustrare numerosi argomenti relativi ai certificati in un ambiente del servizio app. Informazioni sul funzionamento delle associazioni di certificati nelle app a tenant singolo in un ambiente del servizio app.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 65fc4ed25b0fd360de8e3b1439d1766485eb2e58
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688650"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificati e Ambiente del servizio app 

L'Ambiente del servizio app (ASE) è una distribuzione del servizio app di Azure che viene eseguita nella rete virtuale di Azure (VNet). Può essere distribuita con un endpoint applicazione accessibile da Internet o un endpoint applicazione che si trova in una rete virtuale. Se l'Ambiente del servizio app viene distribuito con un endpoint accessibile da Internet, la distribuzione viene chiamata Ambiente del servizio app esterno. Se l'Ambiente del servizio app viene distribuito con un endpoint nel VNet, la distribuzione viene chiamata Ambiente del servizio app con bilanciamento del carico interno. Per altre informazioni sull'Ambiente del servizio app con bilanciamento del carico interno, vedere il documento [Creazione e uso di un Ambiente del servizio app con bilanciamento del carico interno](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

L'ambiente del servizio app è un sistema a tenant singolo. Poiché si tratta di un tenant singolo, esistono alcune funzionalità disponibili solo con un Ambiente del servizio app che non sono disponibili nel servizio app multi-tenant. 

## <a name="ilb-ase-certificates"></a>Certificati di Ambiente del servizio app con bilanciamento del carico interno 

Se si usa un Ambiente del servizio app esterno, le app vengono raggiunte a [appname].[asename].p.azurewebsites.net. Per impostazione predefinita tutti gli Ambienti del servizio app, anche gli Ambienti del servizio App ILB, vengono creati con i certificati che seguono tale formato. Quando si dispone di un Ambiente del servizio app con bilanciamento del carico interno, le app vengono raggiunte in base al nome di dominio specificato durante la creazione dell'Ambiente del servizio app con bilanciamento del carico interno. Affinché le app supportino SSL, è necessario caricare i certificati. Ottenere un certificato SSL valido usando le autorità di certificazione interne, acquistando un certificato da un'autorità di certificazione esterna o usando un certificato autofirmato. 

Sono disponibili due opzioni di configurazione dei certificati con l'Ambiente del servizio app con bilanciamento del carico interno.  È possibile impostare un certificato predefinito con caratteri jolly per l'Ambiente del servizio app con bilanciamento del carico interno o impostare i certificati per le singole app Web nell'Ambiente del servizio app.  Indipendentemente dalla scelta, è necessario configurare correttamente gli attributi del certificato seguenti:

- **Soggetto**: questo attributo deve essere impostato su *.[your-root-domain-here] per un certificato con caratteri jolly dell'Ambiente del servizio app con bilanciamento del carico interno. Se si crea il certificato per l'app, dovrà quindi essere [appname].[your-root-domain-here]
- **Nome alternativo soggetto**: questo attributo deve includere *.[your-root-domain-here] e *.scm.[your-root-domain-here] per il certificato con caratteri jolly dell'Ambiente del servizio app con bilanciamento del carico interno. Se si crea il certificato per l'app, dovrà quindi essere [appname].[your-root-domain-here] e [appname].scm.[your-root-domain-here].

Come terza variante, è possibile creare un certificato Ambiente del servizio app con bilanciamento del carico interno che include tutti i nomi delle singole app nella rete SAN del certificato anziché un riferimento con caratteri jolly. Il problema con questo metodo è che è necessario conoscere in anticipo i nomi delle app che vengono inserite nell'Ambiente del servizio app o è necessario mantenere l'aggiornamento del certificato di Ambiente del servizio app con bilanciamento del carico interno.

### <a name="upload-certificate-to-ilb-ase"></a>Caricare il certificato sull'Ambiente del servizio app con bilanciamento del carico interno 

Dopo aver creato un Ambiente del servizio app con bilanciamento del carico interno nel portale, il certificato deve essere impostato per l'Ambiente del servizio app con bilanciamento del carico interno. Fino a quando il certificato è impostato, nell'Ambiente del servizio app verrà visualizzato un banner che indica che il certificato non è stato impostato.  

Il certificato caricato deve essere un file con estensione .pfx. Dopo aver caricato il certificato, l'Ambiente del servizio app eseguirà un'operazione di scalabilità per impostare il certificato. 

Non è possibile creare l'Ambiente del servizio app e caricare il certificato come una sola azione nel portale o persino in un singolo modello. Come azione separata, è possibile caricare il certificato usando un modello come descritto nel documento [Creare un Ambiente del servizio app da un modello](./create-from-template.md).  

Se si desidera creare rapidamente un certificato autofirmato per il test, è possibile usare il seguente bit di PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Quando si crea un certificato autofirmato, è necessario assicurarsi che il nome del soggetto abbia il formato CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certificati delle applicazioni 

Le app ospitate in un Ambiente del servizio app possono usare le funzionalità relative ai certificati basati sull'applicazione che sono disponibili nel servizio app multi-tenant. Queste funzionalità includono:  

- Certificati SNI 
- SSL basato su IP, supportato solo con un Ambiente del servizio app esterno.  Un ambiente del servizio app con bilanciamento del carico interno non supporta SSL basato su IP.
- Certificati di Key Vault ospitato 

Le istruzioni per il caricamento e la gestione di tali certificati sono disponibili in [aggiungere un certificato SSL nel servizio app Azure](../configure-ssl-certificate.md).  Se si stanno configurando i certificati semplicemente in modo da far corrispondere un nome di dominio personalizzato che è stato assegnato all'app Web, allora tali istruzioni sono sufficienti. Se si sta caricando il certificato per un'app Web di ambiente del servizio app con bilanciamento del carico con il nome di dominio predefinito, specificare il sito scm nella rete SAN del certificato come indicato in precedenza. 

## <a name="tls-settings"></a>Impostazioni di TLS 

A livello di app, è possibile configurare l'impostazione di TLS.  

## <a name="private-client-certificate"></a>Certificato client privato 

Un caso d'uso comune è quello in cui l'app viene configurata come client in un modello client-server. Se si protegge il server con un certificato della CA privato, è necessario caricare il certificato client nell'app.  Nelle istruzioni seguenti verranno caricati i certificati truststore dei ruoli di lavoro su cui è in esecuzione l'app. Se si carica il certificato da un'app, è possibile usarlo con le altre app nello stesso piano di servizio app senza caricare nuovamente il certificato.

Per caricare il certificato nell'app dell'Ambiente di servizio app:

1. Generare un file *.cer* per il certificato. 
2. Passare all'app che richiede il certificato nel portale di Azure
3. Passare alle impostazioni SSL nell'app. Fare clic su Carica certificato. Selezionare Pubblico. Selezionare Computer locale. Specificare un nome. Esplorare e selezionare i file *.cer*. Selezionare Carica. 
4. Copiare l'identificazione personale.
5. Passare alle impostazioni applicazione. Creare un'impostazione app WEBSITE_LOAD_ROOT_CERTIFICATES con l'identificazione personale come valore. Se si hanno più certificati, è possibile inserirli nella stessa impostazione separata da virgole e senza spazi vuoti, ad esempio 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Il certificato sarà disponibile per tutte le app nello stesso piano di servizio app come l'app che ha permesso di configurare tale impostazione. Se è necessario che sia disponibile per le app in un piano di servizio app diverso, è necessario ripetere l'operazione di impostazione dell'app in un'app nel piano di servizio app. Per verificare che il certificato sia impostato, passare alla console Kudu ed eseguire il comando seguente nella console di debug di PowerShell:

    dir cert:\localmachine\root

Per eseguire il test, è possibile creare un certificato autofirmato e generare un file *CER* con il comando PowerShell seguente: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

