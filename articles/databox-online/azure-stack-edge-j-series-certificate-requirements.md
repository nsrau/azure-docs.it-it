---
title: Requisiti e risoluzione dei problemi relativi ai certificati con Azure Stack Edge Pro | Microsoft Docs
description: Vengono descritti i requisiti dei certificati e la risoluzione degli errori relativi ai certificati con il dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: e67b507baf1c3271a7fe32318597722e52fd3890
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891375"
---
# <a name="certificate-requirements"></a>Requisiti per i certificati

Questo articolo descrive i requisiti dei certificati che devono essere soddisfatti prima di poter installare i certificati sul dispositivo Azure Stack Edge Pro. I requisiti sono correlati ai certificati PFX, all'autorità emittente, al nome del soggetto del certificato e al nome alternativo del soggetto e agli algoritmi di certificato supportati.

## <a name="certificate-issuing-authority"></a>Autorità emittente di certificati

I requisiti di emissione del certificato sono i seguenti:

* I certificati devono essere emessi da un'autorità di certificazione interna o da un'autorità di certificazione pubblica.

* L'uso di certificati autofirmati non è supportato.

* Il certificato *rilasciato a:* il campo non deve corrispondere al campo *emesso da:* ad eccezione dei certificati CA radice.



## <a name="certificate-algorithms"></a>Algoritmi di certificato

Per gli algoritmi di certificato è necessario soddisfare i requisiti seguenti:

* I certificati devono usare l'algoritmo della chiave RSA.

* L'algoritmo di firma del certificato non può essere SHA1.

* La dimensione minima della chiave è 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Nome soggetto del certificato e nome alternativo del soggetto

I certificati devono avere il nome soggetto e i requisiti dei nomi alternativi del soggetto seguenti:

* È possibile utilizzare un singolo certificato coprendo tutti gli spazi dei nomi nei campi del nome alternativo del soggetto (SAN) del certificato. In alternativa, è possibile utilizzare singoli certificati per ogni spazio dei nomi. Per entrambi gli approcci è necessario usare caratteri jolly per gli endpoint, se necessario, ad esempio Binary Large Object (BLOB).

* Assicurarsi che i nomi del soggetto (nome comune nel nome del soggetto) facciano parte dei nomi alternativi del soggetto nell'estensione del nome alternativo del soggetto.

* È possibile utilizzare un singolo certificato con caratteri jolly che copre tutti gli spazi dei nomi nei campi SAN del certificato.

* Usare la tabella seguente per la creazione di un certificato dell'endpoint:

    |Type |Nome soggetto (SN)  |Nome alternativo del soggetto (SAN)  |Esempio di nome soggetto |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Archiviazione BLOB|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Interfaccia utente locale| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Certificato singolo a più SAN per entrambi gli endpoint|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Nodo|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |Connessione|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate è hardcoded.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>Certificato PFX

I certificati PFX installati nel dispositivo Azure Stack Edge Pro devono soddisfare i requisiti seguenti:

* Quando si ottengono i certificati dall'autorità SSL, assicurarsi di ottenere la catena di firma completa per i certificati.

* Quando si esporta un certificato PFX, verificare di aver selezionato l'opzione **Includi tutti i certificati nella catena, se possibile** .

* Usare un certificato PFX per endpoint, interfaccia utente locale, nodo, VPN e Wi-Fi perché sono necessarie entrambe le chiavi pubbliche e private per Azure Stack Edge Pro. Per la chiave privata deve essere impostato l'attributo chiave del computer locale.

* La crittografia PFX del certificato deve essere 3DES. Si tratta della crittografia predefinita utilizzata durante l'esportazione da un Windows 10 client o da un archivio certificati Windows Server 2016. Per ulteriori informazioni correlate a 3DES, vedere [triple des](https://en.wikipedia.org/wiki/Triple_DES).

* I file PFX del certificato devono avere una *firma digitale* valida e valori *KeyEncipherment* nel campo *utilizzo chiave* .

* Per i file PFX del certificato devono essere presenti i valori *autenticazione server (1.3.6.1.5.5.7.3.1)* e *autenticazione client (1.3.6.1.5.5.7.3.2)* nel campo *utilizzo chiavi avanzato* .

* Le password per tutti i file PFX del certificato devono essere le stesse al momento della distribuzione se si utilizza lo strumento di controllo della conformità Azure Stack. Per altre informazioni, vedere [creare certificati per il Azure stack Edge Pro usando Azure stack strumento di controllo della conformità dell'hub](azure-stack-edge-j-series-create-certificates-tool.md).

* La password del file PFX del certificato deve essere una password complessa. Prendere nota di questa password perché viene usata come parametro di distribuzione.

Per altre informazioni, vedere [esportare certificati PFX con chiave privata](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Passaggi successivi

[Usare i certificati con Azure Stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md)

[Creare certificati per il Azure Stack Edge Pro usando lo strumento di controllo della conformità dell'hub Azure Stack](azure-stack-edge-j-series-create-certificates-tool.md)

[Esportare i certificati PFX con la chiave privata](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Risoluzione degli errori relativi ai certificati](azure-stack-edge-j-series-certificate-troubleshooting.md)