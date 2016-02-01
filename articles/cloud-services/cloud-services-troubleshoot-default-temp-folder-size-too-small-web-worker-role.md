<properties
   pageTitle="Dimensioni della cartella TEMP predefinita ridotte per un ruolo | Microsoft Azure"
   description="Un ruolo del servizio cloud dispone di una quantità limitata di spazio per la cartella TEMP. Questo articolo fornisce alcuni suggerimenti su come evitare l'esaurimento dello spazio."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Dimensioni della cartella TEMP predefinita ridotte per un ruolo di lavoro/Web del servizio cloud

La directory temporanea predefinita di un ruolo Web o di lavoro del servizio cloud ha una dimensione massima di 100 MB, che può esaurirsi. Questo articolo descrive come evitare l'esaurimento dello spazio della directory temporanea.

>[AZURE.NOTE]L'articolo si applica solo ad Azure SDK 1.0 a SDK 1.4 tramite ruoli Web e di lavoro.

## Contattare il supporto tecnico di Azure

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](http://azure.microsoft.com/support/forums/).

In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Perché ho esaurito lo spazio?
Le variabili di ambiente Windows standard, TEMP e TMP, sono disponibili per l'esecuzione del codice nell'applicazione. Sia TEMP che TMP puntano a una singola directory con una dimensione massima di 100 MB. Tutti i dati archiviati in questa directory non sono persistenti nel ciclo di vita del servizio ospitato. Se le istanze del ruolo in un servizio ospitato vengono riciclate, la directory viene pulita.

## Suggerimento per risolvere il problema
Implementare una delle alternative seguenti:

- Configurare una risorsa di archiviazione locale e accedervi direttamente anziché tramite **TEMP** o **TMP**. Per accedere a una risorsa di archiviazione locale dal codice eseguito all'interno dell'applicazione, chiamare il metodo [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Per altre informazioni sull'impostazione delle risorse di archiviazione locale, vedere [Configurare le risorse di archiviazione locale](cloud-services-configure-local-storage-resources.md).

- Configurare una risorsa di archiviazione locale e definire le directory TEMP e TMP in modo che puntino al percorso della risorsa di archiviazione locale. Questa modifica deve essere eseguita nel metodo [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

L'esempio di codice seguente illustra come modificare le directory di destinazione per TEMP e TMP nel metodo OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## Passaggi successivi

Vedere un altro blog che descrive [come aumentare le dimensioni della cartella temporanea ASP.NET del ruolo Web di Azure](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Altri [articoli sulla risoluzione dei problemi](..\?tag=top-support-issue&service=cloud-services) per i servizi cloud.

Per informazioni su come risolvere i problemi dei ruoli del servizio cloud usando i dati di diagnostica del calcolo Azure PaaS, vedere la [serie di blog di Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0121_2016-->