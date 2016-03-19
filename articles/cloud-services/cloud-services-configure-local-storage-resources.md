<properties
pageTitle="Configurare le risorse di archiviazione locale nei servizi Cloud di Azure"
description=""
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# Configurare le risorse di archiviazione locale

Una risorsa di archiviazione locale è una directory riservata nel file system della macchina virtuale in cui è in esecuzione un'istanza di un ruolo. È possibile archiviare le informazioni nell'istanza della macchina virtuale in modo che il codice in esecuzione nell'istanza possa accedere alla risorsa di archiviazione locale quando è necessario scrivere o leggere da un file. Ad esempio, una risorsa di archiviazione locale può essere utilizzata per memorizzare i dati a cui potrebbe essere necessario accedere nuovamente mentre il servizio è in esecuzione in Azure. È inoltre possibile configurare la risorsa di archiviazione locale per archiviare i file durante l'avvio. Per ulteriori informazioni sulla configurazione delle risorse di archiviazione locale per l'avvio, vedere [Utilizzo dell’archiviazione locale per archiviare i file durante l'avvio](https://msdn.microsoft.com/library/azure/hh974419.aspx)

Una risorsa di archiviazione locale viene dichiarata nel file di definizione del servizio. È possibile dichiarare qualsiasi numero di risorse di archiviazione locale per un ruolo. Ogni risorsa di archiviazione locale è riservata per ogni istanza di tale ruolo. La quantità minima di spazio su disco che è possibile allocare per una risorsa di archiviazione locale è 1 MB. La quantità massima che è possibile allocare per qualsiasi risorsa locale data dipende dalla dimensione della macchina virtuale specificata per il ruolo. Ogni dimensione della macchina virtuale ha un'allocazione di spazio di archiviazione totale corrispondente e lo spazio totale allocato per tutte le risorse di archiviazione locale dichiarate per un ruolo non può superare le dimensioni massime assegnate per la dimensione della macchina virtuale. Per ulteriori informazioni sulla quantità massima di spazio su disco locale assegnato per ogni dimensione della macchina virtuale, vedere [Configurare le dimensioni dei servizi cloud](https://msdn.microsoft.com/library/azure/ee814754.aspx).

> [AZURE.NOTE]
>
-   Tenere presente che è responsabilità dello sviluppatore assicurarsi che la quantità di spazio su disco richiesto per una risorsa di archiviazione locale non superi la quantità massima assegnata per una macchina virtuale. Se si configura una risorsa di archiviazione locale in modo che sia maggiore del valore massimo consentito, non viene generato un errore fino a quando non si tenta un'operazione di scrittura che supera il massimo consentito. In tal caso, l'operazione di scrittura avrà esito negativo e verrà visualizzato un messaggio di errore di spazio su disco insufficiente. Il modello di elaborazione per Azure è di tipo try/fail. Se si riceve un errore di spazio su disco insufficiente, è possibile gestire l'errore e liberare spazio su disco. È quindi possibile ritentare l'operazione di scrittura.
-   È possibile specificare che una risorsa di archiviazione locale venga mantenuta quando un'istanza viene riciclata. Tuttavia, i dati salvati nel file system locale della macchina virtuale non sono garantiti per la durevolezza. Se il ruolo richiede dati durevoli, è consigliabile utilizzare un'unità Azure per archiviare i dati dei file. Le unità di Azure sono supportate dal servizio BLOB di Azure, pertanto la loro durevolezza è garantita.  
>


## Aggiunta di una risorsa di archiviazione locale

Per dichiarare una risorsa di archiviazione locale nel file di definizione del servizio, aggiungere l’elemento **LocalResources** come figlio di un elemento **WebRole** o di un elemento **WorkerRole**. Aggiungere quindi un elemento **LocalStorage** per rappresentare la risorsa. L’elemento **LocalStorage** accetta tre attributi:

-   *nome*
-   *sizeInMB*: specifica la dimensione desiderata per questa risorsa di archiviazione locale
-   *cleanOnRoleRecycle*: specifica se la risorsa di archiviazione locale deve essere cancellata pulito quando un'istanza del ruolo viene riciclata o se deve essere resa persistente nel ciclo di vita del ruolo. Il valore predefinito è **true**.

Il seguente file di definizione del servizio mostra due risorse di archiviazione locale dichiarate per un ruolo Web:

	<?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

Per ulteriori informazioni sui file di definizione del servizio, vedere [Schema di definizione dei servizi di Azure (file .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx).

> [AZURE.NOTE]Se si utilizzano gli strumenti di Azure per Microsoft Visual Studio, è possibile definire una risorsa di archiviazione locale all'interno delle pagine **Properties** per il ruolo. Per ulteriori informazioni, vedere [Configurazione dell'applicazione Azure con Visual Studio](https://msdn.microsoft.com/library/ee405486.aspx).

## Accesso a una risorsa di archiviazione locale a livello di programmazione

Per accedere alla risorsa di archiviazione locale, l'applicazione deve recuperare il percorso dal metodo [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). È quindi possibile utilizzare le operazioni di lettura e scrittura dei file per leggere e scrivere il contenuto della risorsa di archiviazione locale. Nell'esempio seguente viene illustrato come leggere il contenuto di un file denominato **MyTest.txt** dalla risorsa di archiviazione locale e visualizzarlo nella home page di un'applicazione MVC 3:

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## Accesso a una risorsa di archiviazione locale in fase di esecuzione

La libreria gestita di Azure fornisce classi per l'accesso alla risorsa di archiviazione locale all'interno di codice in esecuzione in un'istanza del ruolo. Il metodo [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) restituisce un riferimento a un oggetto denominato [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx).

Poiché l’oggetto **LocalResource** rappresenta una directory, è possibile leggervi e scrivervi utilizzando le classi standard di I/O del file .NET. Per determinare il percorso della directory della risorsa di archiviazione locale, utilizzare la proprietà [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx). Questa proprietà restituisce il percorso completo della risorsa di archiviazione locale, compresa la directory della risorse nominata. Ad esempio, se il servizio è in esecuzione nell'ambiente di sviluppo, la risorsa di archiviazione locale è definita all'interno del file system locale e la proprietà **RootPath** restituirà un valore simile al seguente:


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

Quando il servizio viene distribuito in Azure, il percorso della risorsa di archiviazione locale include l'ID distribuzione e la proprietà **RootPath** restituisce un valore simile al seguente:


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

Il codice in esecuzione un'istanza del ruolo può accedere a una risorsa di archiviazione locale definita per tale ruolo dal momento in cui che l'istanza diventa online al momento in cui viene portata non in linea.

## Passaggi successivi

- [Configurare un servizio cloud per Azure](https://msdn.microsoft.com/library/azure/hh124108.aspx)

<!---HONumber=Oct15_HO3-->