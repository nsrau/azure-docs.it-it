---
title: Creare un ambiente del servizio app di Azure usando un modello di Resource Manager
description: Illustra come creare un ambiente del servizio app di Azure esterno o con bilanciamento del carico interno usando un modello di Resource Manager
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 015bf031aea6b79fcca0a416253e9aa47bb245b6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Creare un ambiente del servizio app usando un modello di Azure Resource Manager

## <a name="overview"></a>Panoramica
È possibile creare gli ambienti del servizio app di Azure con un endpoint accessibile da Internet o un endpoint di un indirizzo interno di una rete virtuale di Azure. Quando viene creato con un endpoint interno, tale endpoint dispone di un componente Azure denominato servizio di bilanciamento del carico interno (ILB). L'ambiente del servizio app in un indirizzo IP interno è chiamato ambiente del servizio app con bilanciamento del carico interno. L'ambiente del servizio app con un endpoint pubblico è chiamato ambiente del servizio app esterno. 

È possibile creare un ambiente del servizio app usando il portale di Azure o un modello di Azure Resource Manager. Questo articolo illustra i passaggi e la sintassi necessari per creare un ambiente del servizio app esterno o con bilanciamento del carico interno con i modelli di Resource Manager. Per informazioni su come creare un ambiente del servizio app nel portale di Azure, vedere [Creare un ambiente del servizio app esterno][MakeExternalASE] o [Creare un ambiente del servizio app con bilanciamento del carico interno][MakeILBASE].

Quando si crea un ambiente del servizio app nel portale di Azure, è possibile creare contemporaneamente la rete virtuale o scegliere una rete virtuale preesistente in cui eseguire la distribuzione. Quando si crea un modello, è necessario iniziare con: 

* Una rete virtuale di Resource Manager.
* una subnet in tale rete virtuale. È consigliabile una subnet per l'ambiente del servizio app con dimensioni di `/25` e 128 indirizzi per supportare la crescita futura. Dopo che l'ambiente del servizio app è stato creato, non è possibile modificarne le dimensioni.
* L'ID risorsa dalla rete virtuale. È possibile ottenere questa informazione dal portale di Azure nelle proprietà della rete virtuale.
* La sottoscrizione in cui si vuole eseguire la distribuzione.
* La località in cui si vuole eseguire la distribuzione.

Per automatizzare la creazione dell'ambiente del servizio app:

1. Creare l'ambiente del servizio app da un modello. Se si crea un ambiente del servizio app esterno, questo è l'ultimo passaggio. Se si crea un ambiente del servizio app con bilanciamento del carico interno, è necessario eseguire ancora alcune operazioni.

2. Dopo avere creato l'ambiente del servizio app con bilanciamento del carico interno, viene caricato un certificato SSL corrispondente al dominio dell'ambiente del servizio app con bilanciamento del carico interno.

3. Il certificato SSL caricato viene assegnato all'ambiente del servizio app ILB come certificato "predefinito".  Questo certificato viene usato per il traffico SSL verso le app nell'ambiente del servizio app con servizio di bilanciamento del carico interno quando usano il dominio radice comune assegnato all'ambiente del servizio app, ad esempio https://someapp.mycustomrootdomain.com.


## <a name="create-the-ase"></a>Creare l'ambiente del servizio app
Un modello di Resource Manager che crea un ambiente del servizio app e il file dei parametri associati sono disponibili [in un esempio][quickstartasev2create] in GitHub.

Per creare un ambiente del servizio app con bilanciamento del carico interno, usare questi [esempi][quickstartilbasecreate] di modello di Resource Manager. I modelli soddisfano questo caso d'uso. La maggior parte dei parametri del file *azuredeploy.parameters.json* è comune alla creazione degli ambienti del servizio app con bilanciamento del carico interno e degli ambienti del servizio app esterni. L'elenco seguente indica parametri importanti o specifici per la creazione di un ambiente del servizio app con bilanciamento del carico interno:

* *internalLoadBalancingMode*: impostare nella maggior parte dei casi su 3, a indicare che sia il traffico HTTP/HTTPS sulle porte 80/443 che le porte dei canali di controllo/dati ascoltate dal servizio FTP nell'ambiente del servizio app saranno associati a un indirizzo interno della rete virtuale allocato dall'ILB. Se questa proprietà viene impostata su 2, solo le porte correlate al servizio FTP (canali di controllo e dati) vengono associate a un indirizzo del servizio di bilanciamento del carico interno. Il traffico HTTP/HTTPS resta nell'indirizzo VIP pubblico.
* *dnsSuffix*: questo parametro indica il dominio radice predefinito che viene assegnato all'ambiente del servizio app. Nella variante pubblica del servizio app di Azure, il dominio radice predefinito per tutte le app Web è *azurewebsites.net*. Poiché un ambiente del servizio app con servizio di bilanciamento del carico interno è interno alla rete virtuale di un cliente, non ha senso usare il dominio radice predefinito del servizio pubblico. Un ambiente del servizio app con servizio di bilanciamento del carico interno deve invece avere un dominio radice predefinito appropriato per la rete virtuale interna dell'azienda. Un'azienda Contoso Corporation potrebbe ad esempio usare un dominio radice predefinito *internal-contoso.com* per le app che devono essere risolvibili e accessibili solo nella rete virtuale di Contoso. 
* *ipSslAddressCount*: questo parametro viene automaticamente impostato sul valore predefinito 0 nel file *azuredeploy.json* perché gli ambienti del servizio app con servizio di bilanciamento del carico interno hanno un solo indirizzo del servizio di bilanciamento del carico interno. Non esistono indirizzi IP SSL per un ambiente del servizio app con bilanciamento del carico interno. Il pool di indirizzi IP SSL per un ambiente del servizio app con bilanciamento del carico interno deve quindi essere impostato su zero. In caso contrario, si verifica un errore di provisioning. 

Dopo la compilazione di *azuredeploy.parameters.json*, creare l'ambiente del servizio app usando il frammento di codice di PowerShell. Modificare i percorsi dei file per fare in modo che corrispondano alle posizioni dei file dei modelli di Resource Manager nel computer. Indicare i valori per il nome della distribuzione di Resource Manager e il nome del gruppo di risorse:

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

La creazione dell'ambiente del servizio app richiede circa un'ora. L'ambiente del servizio app viene quindi visualizzato nel portale nell'elenco di ambienti del servizio app per la sottoscrizione che ha attivato la distribuzione.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Caricare e configurare il certificato SSL "predefinito"
È necessario associare un certificato SSL all'ambiente del servizio app come certificato SSL "predefinito" usato per stabilire le connessioni SSL alle app. Se il suffisso DNS predefinito dell'ambiente del servizio app è *internal-contoso.com*, una connessione a https://some-random-app.internal-contoso.com richiede un certificato SSL valido per **.internal-contoso.com*. 

Ottenere un certificato SSL valido usando le autorità di certificazione interne, acquistando un certificato da un'autorità di certificazione esterna o usando un certificato autofirmato. Indipendentemente dall'origine del certificato SSL è necessario configurare correttamente gli attributi del certificato seguenti:

* **Soggetto**: questo attributo deve essere impostato su **.your-root-domain-here.com*.
* **Nome alternativo soggetto**: questo attributo deve includere sia **.your-root-domain-here.com* che **.scm.your-root-domain-here.com*. Le connessioni SSL al sito SCM/Kudu associato a ogni app usano un indirizzo nel formato *your-app-name.scm.your-root-domain-here.com*.

Dopo aver ottenuto un certificato SSL valido sono necessari altri due passaggi preliminari. Convertire/Salvare il certificato SSL come file con estensione pfx. Tenere presente che il file con estensione pfx deve includere tutti i certificati intermedi e quelli radice. Proteggerlo con una password.

Il file con estensione pfx deve essere convertito in una stringa Base 64 perché il certificato SSL viene caricato usando un modello di Resource Manager. Poiché i modelli di Resource Manager sono file di testo, il file PFX deve essere convertito in una stringa Base 64. In questo modo può essere incluso come parametro del modello.

Usare il frammento di codice di PowerShell seguente per:

* Generare un certificato autofirmato.
* Esportare il certificato come file PFX.
* Convertire il file PXF in una stringa con codifica Base 64.
* Salvare la stringa con codifica Base 64 in un file separato. 

Questo codice PowerShell per la codifica Base 64 è stato adattato dal [blog di script di PowerShell][examplebase64encoding]:

        $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

        $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
        $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

        $fileName = "exportedcert.pfx"
        Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

        $fileContentBytes = get-content -encoding byte $fileName
        $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
        $fileContentEncoded | set-content ($fileName + ".b64")

Dopo avere generato il certificato SSL e averlo convertito in una stringa con codifica Base 64, usare il modello di esempio di Resource Manager disponibile in GitHub, [Configure the default SSL certificate][quickstartconfiguressl] (Configurare il certificato SSL predefinito). 

I parametri del file *azuredeploy.parameters.json* sono elencati qui:

* *appServiceEnvironmentName*: nome dell'ambiente del servizio app con servizio di bilanciamento del carico interno in fase di configurazione.
* *existingAseLocation*: stringa di testo contenente l'area di Azure in cui è stato distribuito l'ambiente del servizio app con servizio di bilanciamento del carico interno.  Ad esempio "Stati Uniti centro-meridionali".
* *pfxBlobString*: rappresentazione del file con estensione pfx sotto forma di stringa con codifica Base 64. Usare il frammento di codice visualizzato prima e copiare la stringa contenuta in "exportedcert.pfx.b64". Incollarla come valore dell'attributo *pfxBlobString*.
* *password*: password usata per la protezione del file con estensione pfx.
* *certificateThumbprint*: identificazione personale del certificato. Se si recupera questo valore da PowerShell, ad esempio *$certificate.Thumbprint* nel frammento di codice precedente, è possibile usare il valore così com'è. Se si copia il valore dalla finestra di dialogo del certificato di Windows, rimuovere gli spazi estranei. Il valore di *certificateThumbprint* sarà simile a AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: identificatore di stringa descrittivo scelto dall'utente per identificare il certificato. Il nome viene usato nell'ambito dell'identificatore univoco di Resource Manager per l'entità *Microsoft.Web/certificates* che rappresenta il certificato SSL. Il nome *deve* terminare con il suffisso seguente: \_yourASENameHere_InternalLoadBalancingASE. Il portale di Azure usa questo suffisso per indicare che il certificato è usato per la protezione di un ambiente del servizio app abilitato al bilanciamento del carico interno.

Un esempio abbreviato di *azuredeploy.parameters.json* è illustrato qui:

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Dopo la compilazione di *azuredeploy.parameters.json*, configurare il certificato SSL predefinito usando il frammento di codice di PowerShell. Modificare i percorsi dei file per fare in modo che corrispondano al percorso dei file dei modelli di Resource Manager nel computer. Indicare i valori per il nome della distribuzione di Resource Manager e il nome del gruppo di risorse:

     $templatePath="PATH\azuredeploy.json"
     $parameterPath="PATH\azuredeploy.parameters.json"

     New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

L'applicazione della modifica richiede circa 40 minuti per ogni front-end dell'ambiente del servizio app. Per un ambiente del servizio app di dimensioni predefinite che usa due front-end, ad esempio, l'operazione richiede all'incirca un'ora e 20 minuti. Durante l'esecuzione del modello, l'ambiente del servizio app non può essere ridimensionato.  

Quando il modello è completato, le app nell'ambiente del servizio app con bilanciamento del carico interno sono accessibili tramite HTTPS. Le connessioni vengono protette usando il certificato SSL predefinito. Il certificato SSL predefinito viene usato quando le app nell'ambiente del servizio app con servizio di bilanciamento del carico interno vengono indirizzate usando una combinazione di nome applicazione e nome host predefinito. https://mycustomapp.internal-contoso.com, ad esempio, usa il certificato SSL predefinito per **.internal-contoso.com*.

Tuttavia, come per le app eseguite nel servizio multi-tenant pubblico, gli sviluppatori possono configurare nomi host personalizzati per le singole app. Possono anche configurare associazioni di certificati SSL basati su SNI univoche per le singole app.

## <a name="app-service-environment-v1"></a>Ambiente del servizio app 1 ##
Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Le informazioni sopra riportate si basano sulla versione ASEv2. Questa sezione illustra le differenze tra ASEv1 e ASEv2.

In ASEv1 si gestiscono manualmente tutte le risorse, inclusi front-end, ruoli di lavoro e indirizzi IP usati per la connessione SSL basata su IP. Per poter aumentare il numero di istanze di un piano di servizio app, è necessario aumentare il numero di istanze del pool di lavoro in cui si vuole ospitare il piano.

ASEv1 usa un modello tariffario diverso rispetto a ASEv2. Nella versione ASEv1, in particolare, si paga per tutti i vCPU allocati, inclusi i vCPU usati per i front-end o i ruoli di lavoro in cui non sono ospitati carichi di lavoro. In ASEv1 la dimensione massima predefinita di un ambiente del servizio app è di 55 host complessivi, inclusi ruoli di lavoro e front-end. Un vantaggio di ASEv1 è quello di poter essere distribuito in una rete virtuale classica e in una rete virtuale di Resource Manager. Per altre informazioni sull'ambiente del servizio app 1, vedere [Introduzione all'ambiente del servizio app 1][ASEv1Intro].

Per creare un ambiente del servizio app 1 usando un modello di Resource Manager, vedere [Creare un ambiente del servizio app con bilanciamento del carico interno 1 con un modello di Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
