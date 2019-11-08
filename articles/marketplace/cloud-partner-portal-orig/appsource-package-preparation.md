---
title: Preparazione pacchetto AppSource | Azure Marketplace
description: Questo articolo descrive come preparare e compilare pacchetti AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2c6b78e62afb43562910c872d31e2c9f564040da
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806106"
---
# <a name="appsource-package-preparation"></a>Preparazione di pacchetti AppSource

Oltre a un file solution.zip, è necessario un **pacchetto AppSource**, ovvero di un file con estensione zip che include tutti gli asset necessari per automatizzare il processo di distribuzione della soluzione nell'ambiente CRM dei clienti. Il **pacchetto AppSource**  include quanto segue:

* Pacchetti per Package Deployer
* File **Content_Types.XML** con le risorse in uso
* File XML con i dati specifici dell'app
* Logo 32x32 che viene visualizzato con l'inserzione nell'interfaccia di amministrazione
* Condizioni di licenza, informativa sulla privacy

La procedura seguente facilita la creazione del pacchetto AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Creare un pacchetto per Package Deployer

Il pacchetto per Package Deployer è una parte del pacchetto AppSource.

Per creare un pacchetto per Package Deployer, usare le istruzioni seguenti: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Al termine dell'esercitazione, il pacchetto sarà costituito dagli asset seguenti:

1. Cartella del pacchetto: contiene tutte le soluzioni, i dati di configurazione, i file flat e il contenuto del pacchetto. _Nota: nell'esempio seguente si presuppone che la cartella del pacchetto sia chiamata "PkgFolder"._
2. Dll: l'assembly che contiene il codice personalizzato del pacchetto. _Nota: nell'esempio seguente si presuppone che il file sia chiamato "MicrosoftSample.dll"._

A questo punto è necessario creare un file denominato "**Content_Types.xml**", che elenca tutte le estensioni degli asset che fanno parte del pacchetto. Di seguito è indicato il codice di esempio del file.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

Il passaggio finale consiste nel comprimere quanto segue in un unico file, che viene chiamato **package.zip**. Il file contiene

1. Cartella PkgFolder (include tutti gli elementi all'interno della cartella)
2. Libreria dll
3. File **Content_Types.xml**

Passaggi per la creazione di package.zip:

1. Inserire la cartella del pacchetto, il file **Content_Types.xml** e la libreria PackageName.dll in una sola directory.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Selezionare tutti gli elementi nella cartella, fare clic con il pulsante destro del mouse e scegliere Invia a Cartella compressa

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Modificare il nome in package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Creare un pacchetto AppSource

Il pacchetto AppSource richiede alcuni file aggiuntivi.

1. jpg (risoluzione 32x32)
2. HTML (file in formato HTML)
3. File **Content_Types.XML** (come sopra)
4. xml

Di seguito è indicato il codice di esempio del file input.xml. Vedere le definizioni nella tabella seguente.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Dove:**

|Campo|Dettagli|
|---|---|
|ProviderName|Il nome di chi fornisce la soluzione. Se si tratta di un team Microsoft, dovrebbe contenere Microsoft.|
|PackageFile |Gli asset di Package Deployer compressi in un file content\_types.xml. Questo file con estensione zip deve contenere l'assembly di Package Deployer e la cartella con gli asset di Package Deployer, vale a dire package.zip.|
|SolutionAnchorName |Nome del file della soluzione con estensione zip in Package Deployer, che viene usato per il nome visualizzato e la descrizione degli asset della soluzione.|
| StartDate| Data a partire dalla quale il pacchetto della soluzione è disponibile. Il formato è MM/GG/AAAA.|
|EndDate|Data a partire dalla quale il pacchetto della soluzione non è più disponibile. Il formato è MM/GG/AAAA. |
|SupportedCountries |Si tratta di un elenco delimitato da virgole di paesi/aree geografiche che dovrebbero visualizzare questo pacchetto. Contattare i servizi online per un elenco di tutti i codici paese correnti. Al momento della stesura del presente articolo, l'elenco è il seguente: AE,AL,AM,AO,AR,AT,AU,AZ,BA,BB,BD,BE,BG,BH,BM,BN,BO,BR,BY,CA,CH,CI,CL,CM,CO,CR,CV,CW,CY,CZ,DE,DK,DO,DZ,EC,EE,EG,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZA,ZW |
|LearnMoreLink | URL della pagina che contiene altre informazioni sul pacchetto. |
|Locales|Un'istanza di questo nodo per ogni lingua dell'esperienza utente che si vuole supportare nell'esperienza utente della soluzione preferita. Questo nodo contiene elementi figlio che descrivono le impostazioni locali, il logo e i termini di ogni lingua.|
|Locales: PackageLocale.Code|Identificatore delle impostazioni locali della lingua di questo nodo. Esempio: Inglese (Stati Uniti) corrisponde a 1033.|
|Locales: PackageLocale.IsDefault|Indica che questa è la lingua predefinita. Viene usata come lingua di fallback se quella dell'esperienza utente scelta dal cliente non è disponibile.|
|Locales: Logo|Logo che si vuole usare per questo pacchetto. Le dimensioni dell'icona sono 32x32. Sono consentiti i formati PNG e JPG.|
|Locales:Terms: PackageTerm.File|Nome file del documento HTML che contiene le condizioni di licenza.|

Ecco dove viene visualizzato il logo:

![CRMScreenShot5](media/CRMScreenShot5.png)

Il passaggio finale consiste nel comprimere quanto segue in un unico file,

1. con estensione zip (creato in precedenza)
2. File **Content_Types.xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Rinominare il file con un nome appropriato per l'app. È preferibile includere il nome della società e il nome dell'app. Ad esempio: **_Microsoft_SamplePackage.zip**.
