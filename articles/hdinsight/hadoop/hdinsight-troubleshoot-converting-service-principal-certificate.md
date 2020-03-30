---
title: Conversione del contenuto del certificato in base 64 - Azure HDInsightConverting certificate contents to base-64 - Azure HDInsight
description: Conversione del contenuto del certificato dell'entità servizio nel formato stringa codificato base 64 in Azure HDInsightConverting service principal certificate contents to base-64 encoded string format in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894179"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Conversione del contenuto del certificato dell'entità servizio nel formato di stringa codificato base 64 in HDInsightConverting service principal certificate contents to base-64 encoded string format in HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Viene visualizzato un messaggio di errore che indica che l'input non è una stringa Base 64 valida in quanto contiene un carattere non base 64, più di due caratteri di spaziatura interna o un carattere di spazio vuoto non vuoto tra i caratteri di spaziatura interna.

## <a name="cause"></a>Causa

Quando si usa la distribuzione di modelli di PowerShell o di Azure per creare cluster con Data Lake come archiviazione primaria o aggiuntiva, il contenuto del certificato dell'entità servizio fornito per accedere all'account di archiviazione di Data Lake è nel formato base 64. La conversione non corretta del contenuto del certificato pfx nella stringa codificata in base 64 può causare questo errore.

## <a name="resolution"></a>Risoluzione

Dopo aver creato il certificato dell'entità servizio in formato pfx (vedere qui per i passaggi di creazione dell'entità servizio di esempio), usare il comando di PowerShell seguente o il frammento di codice C , per convertire il contenuto del certificato in formato base 64.Once you have the service principal certificate in pfx format (see [here](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) for sample service principal creation steps), use the following PowerShell command or C's snippet to convert the certificate contents to base-64 format.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
