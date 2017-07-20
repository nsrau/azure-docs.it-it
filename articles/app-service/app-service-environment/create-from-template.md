---
title: Creazione di un ambiente del servizio app di Azure usando un modello di Resource Manager
description: Viene illustrato come creare un ambiente del servizio app di Azure esterno o ILB usando un modello di Resource Manager
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
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 370ab7a32634f04b4776a2a9a84ecaacf11ea617
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017

---
# <a name="how-to-create-an-ase-using-azure-resource-manager-templates"></a>Come creare un ambiente del servizio app usando modelli di Azure Resource Manager

## <a name="overview"></a>Panoramica
È possibile creare gli ambienti del servizio app con un endpoint accessibile da Internet o un endpoint di un indirizzo interno della rete virtuale di Azure (VNet).  Quando viene creato con un endpoint interno, tale endpoint dispone di un componente Azure denominato servizio di bilanciamento del carico interno (ILB).  L'ambiente del servizio app esterno con un endpoint pubblico viene denominato ASE esterno e l'ambiente del servizio app esterno su un indirizzo IP interno viene denominato ambiente del servizio app ILB.  

È possibile creare un ambiente del servizio app usando il portale Azure o i modelli di Azure Resource Manager.  Questo articolo illustra i passaggi e la sintassi necessari per creare un ambiente del servizio app esterno o ILB con i modelli di Azure Resource Manager.  Per altre informazioni sulla creazione di un ambiente del servizio app nel portale è possibile iniziare con la [creazione di un ambiente del servizio app esterno] [ MakeExternalASE] o la [creazione di un ambiente del servizio app ILB][MakeILBASE].

Quando si crea un ambiente del servizio app nel portale è possibile scegliere di creare contemporaneamente la propria rete virtuale o di selezionare una rete virtuale già esistente per eseguire la distribuzione al suo interno.  Nella creazione da un modello è necessario iniziare con: 
* una rete virtuale di Resource Manager
* una subnet in tale rete virtuale.  È consigliabile che la subnet dell'ambiente del servizio app sia di tipo /25 con 128 indirizzi in modo da supportare lo sviluppo futuro.  Non è possibile apportare modifiche dopo la creazione dell'ambiente del servizio app
* L'id di risorsa dalla propria rete virtuale.  È possibile ottenerlo dal portale di Azure nelle proprietà di rete virtuale di Azure
* La sottoscrizione in cui si desidera eseguire la distribuzione
* La posizione in cui si desidera eseguire la distribuzione

Per automatizzare la creazione dell'ambiente del servizio app:

1. Creare l'ambiente del servizio app da un modello.  Se viene creato un ambiente del servizio app esterno, è sufficiente questo passaggio.  Se viene creato un ambiente del servizio app ILB è necessario eseguire alcune operazioni aggiuntive
2. Dopo aver creato l'ambiente del servizio app ILB, viene caricato un certificato SSL corrispondente al dominio dell'ambiente del servizio app ILB.
3. Il certificato SSL caricato viene assegnato all'ambiente del servizio app ILB come certificato "predefinito".  Questo certificato SSL verrà usato per il traffico SSL verso le app nell'ambiente del servizio app con servizio di bilanciamento del carico interno quando si usa il dominio radice comune assegnato all'ambiente del servizio app, ad esempio https://someapp.mycustomrootcomain.com.


## <a name="creating-the-ase"></a>Creazione dell'ambiente del servizio app
Un modello di Azure Resource Manager di esempio che crea un ambiente del servizio app e il file dei parametri associati sono disponibili in GitHub [qui][quickstartasev2create].

Se si desidera creare un ambiente del servizio app ILB, si dovrebbero usare invece i modelli di Resource Manager [qui][quickstartilbasecreate].  I modelli soddisfano questo caso d'uso.  La maggior parte dei parametri del file *azuredeploy.parameters.json* è comune alla creazione sia dell'ambiente del servizio app ILB che dell'ambiente del servizio app esterno.  L'elenco seguente indica parametri importanti o specifici per la creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno:

* *interalLoadBalancingMode*: impostare nella maggior parte dei casi su 3, a indicare che sia il traffico HTTP/HTTPS sulle porte 80/443 che le porte dei canali di controllo/dati ascoltati dal servizio FTP nell'ambiente del servizio app saranno associati a un indirizzo interno della rete virtuale allocato dal servizio di bilanciamento del carico interno.  Se questa proprietà viene invece impostata su 2, solo le porte correlate al servizio FTP, ovvero i canali di controllo e i canali dei dati, saranno associate a un indirizzo del servizio di bilanciamento del carico interno, mentre il traffico HTTP/HTTPS rimarrà associato all'indirizzo VIP pubblico.
* *dnsSuffix*: questo parametro indica il dominio radice predefinito che verrà assegnato all'ambiente del servizio app.  Nella variante pubblica del servizio app di Azure, il dominio radice predefinito per tutte le app Web è *azurewebsites.net*.  Poiché tuttavia un ambiente del servizio app con servizio di bilanciamento del carico interno è interno alla rete virtuale di un cliente, non ha senso usare il dominio radice predefinito del servizio pubblico.  Un ambiente del servizio app con servizio di bilanciamento del carico interno deve invece avere un dominio radice predefinito appropriato per la rete virtuale interna dell'azienda.  Un'ipotetica azienda Contoso Corporation potrebbe ad esempio usare un dominio radice predefinito *internal-contoso.com* per le app che devono essere risolvibili e accessibili solo nella rete virtuale di Contoso. 
* *ipSslAddressCount*: questo parametro viene automaticamente impostato sul valore predefinito di 0 nel file *azuredeploy.json* perché gli ambienti del servizio app con servizio di bilanciamento del carico interno hanno un solo indirizzo del servizio di bilanciamento del carico interno.  Non sono disponibili indirizzi IP SSL espliciti per un ambiente del servizio app con servizio di bilanciamento del carico interno, quindi il pool di indirizzi IP SSL per un ambiente del servizio app con servizio di bilanciamento del carico interno deve essere impostato su zero, in caso contrario si verificherà un errore di provisioning. 

Dopo che il file *azuredeploy.parameters.json* è stato compilato, l'ambiente del servizio app potrà essere creato con il seguente frammento di codice Powershell.  Modificare i parametri PATH dei file per fare in modo che corrispondano al percorso dei file dei modelli di Azure Resource Manager nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo aver inviato il modello di Azure Resource Manager, la creazione dell'ambiente del servizio app richiederà circa un'ora.  Al termine della creazione, l'ambiente del servizio app verrà visualizzato nel portale nell'elenco di ambienti del servizio app per la sottoscrizione che ha attivato la distribuzione.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Caricamento e configurazione del certificato SSL "predefinito"
Dopo aver creato l'ambiente del servizio app con servizio di bilanciamento del carico interno è necessario associare un certificato SSL all'ambiente del servizio app come certificato SSL "predefinito", per stabilire le connessioni SSL alle app.  Proseguendo con l'esempio dell'ipotetica Contoso Corporation, se il suffisso DNS predefinito dell'ambiente del servizio app è *internal-contoso.com*, una connessione a *https://some-random-app.internal-contoso.com* richiede un certificato SSL valido per **.internal-contoso.com*. 

Esistono diversi modi per ottenere un certificato SSL valido, tra cui CA interne, l'acquisto di un certificato da un'autorità di certificazione esterna e l'uso di un certificato autofirmato.  Indipendentemente dall'origine del certificato SSL è necessario configurare correttamente gli attributi del certificato seguenti:

* *Subject*: questo attributo deve essere impostato su **.your-root-domain-here.com*.
* *Nome alternativo soggetto*: questo attributo deve includere sia **.your-root-domain-here.com* che **.scm.your-root-domain-here.com*.  La seconda voce è necessaria perché le connessioni SSL al sito SCM/Kudu associato a ogni app verranno eseguite con un indirizzo nel formato *your-app-name.scm.your-root-domain-here.com*.

Dopo aver ottenuto un certificato SSL valido sono necessari altri due passaggi preliminari.  Il certificato SSL deve essere convertito/salvato come file con estensione pfx.  Il file con estensione pfx deve includere tutti i certificati intermedi e radice ed essere protetto con una password.

Il file con estensione pfx dovrà quindi essere convertito in una stringa con codifica Base64 perché il certificato SSL verrà caricato usando un modello di Azure Resource Manager.  Poiché i modelli di Azure Resource Manager sono file di testo, il file con estensione pfx deve essere convertito in una stringa con codifica Base64 per essere incluso come parametro del modello.

Il frammento di codice Powershell riportato di seguito illustra un esempio di generazione di un certificato autofirmato, esportazione del certificato come file con estensione pfx, conversione del file con estensione pfx in una stringa con codifica Base64 e quindi salvataggio della stringa con codifica Base64 in un file separato.  Il codice Powershell per la codifica Base64 è stato adattato dal [blog di script di Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Dopo aver generato il certificato SSL e averlo convertito in una stringa con codifica Base64, è possibile usare il modello di esempio di Azure Resource Manager disponibile in GitHub per la [configurazione del certificato SSL predefinito][quickstartconfiguressl].

I parametri del file *azuredeploy.parameters.json* sono elencati di seguito:

* *appServiceEnvironmentName*: nome dell'ambiente del servizio app con servizio di bilanciamento del carico interno in fase di configurazione.
* *existingAseLocation*: stringa di testo contenente l'area di Azure in cui è stato distribuito l'ambiente del servizio app con servizio di bilanciamento del carico interno.  Ad esempio "Stati Uniti centro-meridionali".
* *pfxBlobString*: rappresentazione del file con estensione pfx sotto forma di stringa con codifica Base64.  Usando il frammento di codice indicato in precedenza, la stringa contenuta in "exportedcert.pfx.b64" dovrà essere copiata e incollata come valore dell'attributo *pfxBlobString* .
* *password*: password usata per la protezione del file con estensione pfx.
* *certificateThumbprint*: identificazione personale del certificato.  Se si recupera questo valore da Powershell, ad esempio *$certificate.Thumbprint* nel frammento di codice precedente, è possibile usare il valore così com'è.  Se tuttavia si copia il valore dalla finestra di dialogo del certificato di Windows, rimuovere gli spazi estranei.  Il valore di *certificateThumbprint* sarà simile a AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: identificatore di stringa descrittivo scelto dall'utente per identificare il certificato.  Il nome viene usato nell'ambito dell'identificatore univoco di Azure Resource Manager per l'entità *Microsoft.Web/certificates* che rappresenta il certificato SSL.  Il nome **deve** terminare con il suffisso seguente: \_yourASENameHere_InternalLoadBalancingASE.  Questo suffisso viene usato dal portale per indicare che il certificato è usato per la protezione di un ambiente del servizio app abilitato al bilanciamento del carico interno.

Un esempio abbreviato di *azuredeploy.parameters.json* è illustrato di seguito:

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

Dopo che il file *azuredeploy.parameters.json* è stato compilato, il certificato SSL predefinito può essere configurato con il frammento di codice Powershell seguente.  Modificare i parametri PATH dei file per fare in modo che corrispondano al percorso dei file dei modelli di Azure Resource Manager nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo l'invio del modello di Azure Resource Manager, l'applicazione della modifica richiederà all'incirca 40 minuti per ogni front-end dell'ambiente del servizio app.  Per un ambiente del servizio app di dimensioni predefinite che usa due front-end, ad esempio, l'operazione richiederà all'incirca un'ora e venti minuti.  Durante l'esecuzione del modello, l'ambiente del servizio app non potrà essere ridimensionato.  

Al termine dell'esecuzione del modello sarà possibile accedere alle app nell'ambiente del servizio app con servizio di bilanciamento del carico interno tramite HTTPS e le connessioni saranno protette con il certificato SSL predefinito.  Il certificato SSL predefinito verrà usato quando le app nell'ambiente del servizio app con servizio di bilanciamento del carico interno vengono indirizzate usando una combinazione di nome applicazione e nome host predefinito.  *https://mycustomapp.internal-contoso.com*, ad esempio, userà il certificato SSL predefinito per **.internal-contoso.com*.

Proprio come le app in esecuzione nel servizio multi-tenant pubblico, gli sviluppatori possono tuttavia anche configurare nomi host personalizzati e associazioni univoche a certificati SNI SSL per le singole app.  

## <a name="asev1"></a>ASEv1 ##
Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Le informazioni sopra riportate facevano riferimento alla versione ASEv2. Questa sezione illustra le differenze tra ASEv1 e ASEv2.

In ASEv1 è necessario gestire manualmente tutte le risorse, inclusi front-end, ruoli di lavoro e indirizzi IP usati per la connessione SSL basata su IP. Per poter aumentare il numero di istanze di un piano di servizio app, è necessario prima aumentare il numero di istanze del pool di lavoro in cui si vuole ospitare il piano.

ASEv1 usa un modello tariffario diverso rispetto a ASEv2. Nella versione ASEv1, in particolare, è necessario pagare per tutti i core allocati, inclusi i core usati per i front-end o i ruoli di lavoro in cui non sono ospitati carichi di lavoro. In ASEv1, la dimensione massima predefinita per un piano in un ambiente del servizio app è di 55 host complessivi, inclusi ruoli di lavoro e front-end. ASEv1 ha anche il vantaggio di poter essere distribuito sia in una rete virtuale classica sia in una rete virtuale di Resource Manager. Per altre informazioni su ASEv1, vedere: [Introduzione all'ambiente del servizio app v1][ASEv1Intro]

Se si desidera creare un ASEv1 utilizzando un modello di Resource Manager è possibile iniziare qui con [creazione di un ambiente del servizio app ILB v1 con un modello di Resource Manager][ILBASEv1Template].


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
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: ../../app-service-web/app-service-app-service-environment-create-ilb-ase-resourcemanager.md

