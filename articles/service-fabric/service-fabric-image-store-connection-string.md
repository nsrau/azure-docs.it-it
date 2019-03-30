---
title: Stringa di connessione dell'archivio immagini di Azure Service Fabric | Microsoft Docs
description: Informazioni sulla stringa di connessione dell'archivio immagini
services: service-fabric
documentationcenter: .net
author: alexwun
manager: chackdan
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 4a56b48c0041e963b89312c59335b45cabacc1bb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667550"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Informazioni sull'impostazione ImageStoreConnectionString

In alcuni documenti di Microsoft si accenna brevemente all'esistenza di un parametro "ImageStoreConnectionString" senza che descriverne il significato reale. E dopo aver consultato l'articolo [Distribuire e rimuovere applicazioni con PowerShell][10] sembra che sia sufficiente copiare e incollare il valore così come viene visualizzato nel manifesto del cluster del cluster di destinazione. L'impostazione quindi deve essere configurabile per ogni cluster, ma quando si crea un cluster nel [Portale di Azure][11], non è possibile configurare questa impostazione che è sempre "fabric:ImageStore". Qual è lo scopo di questa impostazione quindi?

![Manifesto del cluster][img_cm]

Service Fabric inizialmente era una piattaforma usata da diversi team di Microsofti, pertanto alcuni aspetti sono altamente personalizzabili e "Archivio immagini" è uni di questi aspetti. In pratica, l'Archivio di immagini è un repository collegabile per l'archiviazione dei pacchetti di applicazioni. Quando l'applicazione viene distribuita in un nodo del cluster, tale nodo scarica il contenuto del pacchetto dell'applicazione da Archivio immagini. ImageStoreConnectionString è un'impostazione che include tutte le informazioni necessarie sia per i client che per i nodi utile a trovare l'Archivio immagini corretto per un determinato cluster.

Esistono attualmente tre tipi possibili di provider i Archivio immagini e le relative stringhe di connessione sono le seguenti:

1. Servizio di archiviazione immagini: "fabric:ImageStore"

2. File System: "file:[file system path]"

3. Archiviazione di Azure: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

Il tipo di provider utilizzato nell'ambiente di produzione è il Servizio di archiviazione immagini, che è un servizio di sistema persistente con stato che è possibile visualizzare da Service Fabric Explorer. 

![Servizio di archiviazione immagini][img_is]

Ospitare Archivio immagini in un servizio di sistema all'interno del cluster stesso elimina le dipendenze esterne per il repository del pacchetto e offre un maggiore controllo sulla posizione di archiviazione. È probabile che i miglioramenti futuri di Archivio immagini riguarderanno prima di tutto, o in esclusivamente, il provider di Archivio immagini. La stringa di connessione per il provider del Servizio di archiviazione immagini non dispone di informazioni univoche in quanto il client è già connesso al cluster di destinazione. Per il client è sufficiente sapere che è necessario usare i protocolli per il servizio di sistema.

Il provider di File System viene usato al posto del servizio di archiviazione immagini per i cluster di una casella locali durante lo sviluppo per eseguire il bootstrap del cluster un po' più velocemente. La differenza generalmente è poca, ma è un'ottimizzazione utile a molti durante lo sviluppo. È possibile distribuire un cluster di una casella locale anche con altri tipi di provider per l'archiviazione, ma in genere non è necessario farlo poiché il flusso di lavoro di sviluppo e test rimane invariato indipendentemente dal provider. Il provider di Archiviazione di Azure esiste solo per il supporto legacy dei cluster meno recenti, distribuiti prima dell'introduzione del provider del Servizio di archiviazione immagini.

Inoltre, né il provider di File System né il provider di Archiviazione di Azure devono essere usati come metodo di condivisione di un archivio immagini tra più cluster. Tale operazione determinerebbe il danneggiamento dei dati di configurazione del cluster, perché ogni cluster potrebbe scrivere dati in conflitto nell'archivio immagini. Per condividere i pacchetti dell'applicazione con provisioning tra più cluster, usare i file [sfpkg][12], che possono essere caricati in qualsiasi archivio esterno con un URI di download.

Quindi, sebbene ImageStoreConnectionString sia configurabile, viene semplicemente usata l'impostazione predefinita. Durante la pubblicazione in Azure tramite Visual Studio, il parametro viene impostato automaticamente in base alle esigenze dell'utente. Per la distribuzione a livello di codice ai cluster ospitati in Azure, la stringa di connessione è sempre "fabric:ImageStore". In caso di dubbi, il suo valore può essere verificato sempre recuperando il manifesto del cluster da [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) o [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Sia i test in locale che i cluster di produzione devono essere sempre configurati per poter usare anche il provider del Servizio di archivio immagini.

### <a name="next-steps"></a>Passaggi successivi
[Deploy and remove applications using PowerShell][10] (Distribuire e rimuovere applicazioni con PowerShell)

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
