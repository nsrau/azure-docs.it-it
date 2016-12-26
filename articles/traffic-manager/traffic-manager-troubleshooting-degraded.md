---
title: Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico
description: Come risolvere i problemi relativi ai profili di Gestione traffico quando risulta uno stato Danneggiato.
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 179dc3fa0c1ab534cb1116269832f3bc81c4c434

---

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico

In questo articolo viene descritto come risolvere i problemi relativi al profilo di Gestione traffico di Azure che mostra uno stato danneggiato. Si supponga di aver configurato un profilo di Gestione traffico che punta ad alcuni dei servizi ospitati cloudapp.net. Dopo un controllo, lo stato risulta Danneggiato.

![Stato Danneggiato](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Accedendo alla scheda Endpoint del profilo, uno o più endpoint risulteranno con stato Offline:

![Offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Informazioni sui probe di Gestione traffico

* Gestione traffico considera un endpoint Online solo se il probe ottiene una risposta HTTP 200 dal percorso probe. Qualsiasi risposta diversa da 200 viene considerata un errore.
* Un reindirizzamento 30x avrà esito negativo anche se l'URL reindirizzato restituisce 200.
* Per i probe HTTPS, gli errori di certificati vengono ignorati.
* Il contenuto effettivo del percorso probe non è importante, purché venga restituito 200. Una tecnica comune consiste nell'impostare il percorso su un valore simile a "/favicon.ico". Il contenuto dinamico, ad esempio le pagine ASP, non può sempre restituire 200, anche quando l'applicazione è integra.
* La procedura consigliata prevede di impostare il percorso probe su un valore con logica sufficiente a determinare se il sito sia attivo o non attivo. Nell'esempio precedente, impostando il percorso su "/favicon.ico", si verifica solo che w3wp.exe risponda. Questo probe non indica necessariamente che l'applicazione Web è integra. Un'opzione migliore prevede di impostare un percorso su un valore simile a "/Probe.aspx", che è dotato della logica necessaria a determinare l'integrità del sito. Ad esempio, si potrebbero usare i contatori delle prestazioni per verificare l'utilizzo della CPU o misurare il numero di richieste con esito negativo, o tentare di accedere a risorse come lo stato del database o della sessione per assicurarsi che l'applicazione Web funzioni correttamente.
* Se tutti gli endpoint di un profilo sono danneggiati, Gestione traffico li gestirà come integri e indirizzerà il traffico a tutti gli endpoint. In questo modo si evita che eventuali problemi con il meccanismo di probe non comportino un'interruzione completa del servizio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per risolvere gli errori di probe, è necessario uno strumento che mostri il codice di stato HTTP restituito dall'URL del probe. Sono disponibili numerosi strumenti che mostrano la risposta HTTP non elaborata.

* [Fiddler](http://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

È inoltre possibile utilizzare la scheda Rete in Strumenti F12 per il debug di Internet Explorer per visualizzare le risposte HTTP.

In questo esempio si vuole visualizzare la risposta ricevuta dall'URL del probe: http://watestsdp2008r2.cloudapp.net:80/Probe. L'esempio di PowerShell seguente illustra il problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Output di esempio:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Si noti che è pervenuta una risposta di reindirizzamento. Come indicato in precedenza, qualsiasi StatusCode diverso da 200 viene considerato un errore. Gestione traffico modifica lo stato dell'endpoint impostandolo su Offline. Per risolvere il problema, controllare la configurazione del sito Web per verificare che il percorso probe sia in grado di restituire lo StatusCode appropriato. Riconfigurare quindi il probe di Gestione traffico in modo che punti a un percorso che restituisce un valore di 200.

Se il probe usa il protocollo HTTPS, è necessario disabilitare il controllo del certificato per evitare errori SSL/TLS durante il test. Le seguenti istruzioni di PowerShell disabilitano la convalida dei certificati per la sessione corrente di PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)

[Gestione traffico di Azure](traffic-manager-overview.md)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[App Web di Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlet di Gestione traffico di Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx



<!--HONumber=Nov16_HO5-->


