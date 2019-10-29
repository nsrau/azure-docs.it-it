---
title: Conversione del contenuto del certificato in base-64-Azure HDInsight
description: Conversione del contenuto del certificato dell'entità servizio nel formato stringa con codifica base 64 in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: ad41ef464f592fd09b070ab6f715e9a07fb3cf21
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044502"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Conversione del contenuto del certificato dell'entità servizio nel formato stringa con codifica base 64 in HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Viene visualizzato un messaggio di errore che informa che l'input non è una stringa base 64 valida perché contiene un carattere non base 64, più di due caratteri di riempimento o uno spazio non vuoto tra i caratteri di riempimento.

## <a name="cause"></a>Causa

Quando si usa PowerShell o la distribuzione di modelli di Azure per creare cluster con Data Lake come risorsa di archiviazione primaria o aggiuntiva, il contenuto del certificato dell'entità servizio fornito per accedere al Data Lake account di archiviazione è nel formato base 64. La conversione non corretta del contenuto del certificato pfx in una stringa con codifica base 64 può causare questo errore.

## <a name="resolution"></a>Risoluzione

Una volta ottenuto il certificato dell'entità servizio in formato pfx (vedere [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) per i passaggi di esempio per la creazione dell'entità servizio), C# usare il comando o il frammento di codice di PowerShell seguente per convertire il contenuto del certificato in formato base-64.

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

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* È possibile connettersi con [@AzureSupport](https://twitter.com/azuresupport) , l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
