---
title: 'Esercitazione: Eseguire la migrazione da Google Maps a Mappe di Azure | Mappe di Microsoft Azure'
description: Esercitazione su come eseguire la migrazione da Google Maps a Mappe di Microsoft Azure. Vengono fornite indicazioni dettagliate su come passare alle API e agli SDK di Mappe di Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 175625ab9fca9103bde027c3c0ea0986806ad846
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208303"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Eseguire la migrazione da Google Maps a Mappe di Azure

Questa esercitazione fornisce informazioni dettagliate su come eseguire la migrazione di applicazioni Web, per dispositivi mobili e basate su server da Google Maps alla piattaforma Mappe di Microsoft Azure. L'esercitazione include esempi di codice comparativi, suggerimenti per la migrazione e procedure consigliate per la migrazione a Mappe di Azure.

## <a name="azure-maps-platform-overview"></a>Panoramica della piattaforma Mappe di Azure

Mappe di Azure offre agli sviluppatori di tutti i settori potenti funzionalità geospaziali con dati mappa aggiornati regolarmente, per fornire contesto geografico per le applicazioni Web e per dispositivi mobili. Mappe di Azure è un set di API REST conforme con Azure One API. Le API REST offrono le funzionalità per Rendering mappe, Ricerca, Pianificazione percorso, Informazioni sul traffico, Fusi orari, Georilevazione, Geofencing, Dati mappe, Meteo, Mobilità e operazioni spaziali e includono gli SDK per Android e il Web per lo sviluppo semplificato, flessibile e portabile tra più piattaforme.

## <a name="high-level-platform-comparison"></a>Confronto generale tra piattaforme

La tabella seguente fornisce un elenco generale delle funzionalità di Mappe di Azure corrispondenti alle funzionalità di Google Maps. Questo elenco non mostra tutte le funzionalità di Mappe di Azure. Tra le funzionalità aggiuntive di Mappe di Azure sono incluse accessibilità, geofencing, isocrone, operazioni spaziali, accesso diretto alle tessere mappa, servizi batch e confronti di copertura dei dati (ovvero copertura delle immagini).

| Funzionalità di Google Maps         | Supporto di Mappe di Azure                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Pianificata                                |
| API REST del servizio           | ✓                                      |
| Indicazioni (pianificazione percorso)        | ✓                                      |
| Matrice di distanze             | ✓                                      |
| Altitudine                   | Pianificata                                |
| Geocodifica (diretta/inversa) | ✓                                      |
| Georilevazione                 | N/D                                    |
| Ricerca luoghi               | ✓                                      |
| Dettagli di luoghi              | N/D - sito Web e numero di telefono disponibili |
| Foto di luoghi               | N/D                                    |
| Completamento automatico luoghi          | ✓                                      |
| Mappe statiche                 | ✓                                      |
| Visualizzazione strada statica          | N/D                                    |
| Fuso orario                   | ✓                                      |
| API mappe incorporata           | N/D                                    |
| URL mappa                    | N/D                                    |

Google Maps fornisce l'autenticazione basata su chiavi di base. Mappe di Azure fornisce sia l'autenticazione basata su chiavi di base, sia l'autenticazione di Azure Active Directory. L'autenticazione di Azure Active Directory offre un maggior numero di funzionalità di sicurezza, rispetto all'autenticazione basata su chiavi di base.

## <a name="licensing-considerations"></a>Considerazioni sulla gestione delle licenze

Quando si esegue la migrazione da Google Maps a Mappe di Azure, occorre tenere in considerazione quanto segue in merito alla gestione delle licenze.

- Mappe di Azure addebita i costi relativi all'utilizzo di mappe interattive in base al numero di tessere mappa caricate. Google Maps invece addebita i costi in base al caricamento del controllo mappa. Negli SDK di Mappe di Azure interattivi le tessere mappa vengono automaticamente memorizzate nella cache per ridurre i costi di sviluppo. Viene generata una transazione di Mappe di Azure ogni 15 tessere mappa caricate. Gli SDK interattivi di Mappe di Azure usano tessere da 512 pixel e generano in media una o meno di una transazione per visualizzazione pagina.
- Spesso è molto più conveniente sostituire le immagini mappa statiche dai servizi Web di Google Maps con Azure Maps Web SDK. Azure Maps Web SDK usa le tessere mappa e, a meno che l'utente non esegua operazioni di panoramica e zoom sulla mappa, spesso il servizio genererà solo una frazione di una transazione per caricamento mappa. Azure Maps Web SDK include opzioni per disabilitare la panoramica e lo zoom, se si desidera. Offre inoltre molte più opzioni di visualizzazione rispetto a un servizio Web di mappe statiche.
- Mappe di Azure consente di archiviare in Azure i dati della sua piattaforma. I dati possono anche essere memorizzati nella cache altrove per un massimo di sei mesi, in base alle [condizioni per l'utilizzo](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Ecco alcune risorse correlate per Mappe di Azure:

- [Pagina dei prezzi di Mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Condizioni per l'utilizzo di Mappe di Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluse nelle condizioni per l'utilizzo di Microsoft Online Services)
- [Scegliere il piano tariffario corretto in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Piano di migrazione suggerito

Di seguito è descritto un piano di migrazione generale.

1. Eseguire l'inventario degli SDK e dei servizi di Google Maps usati dall'applicazione. Verificare che Mappe di Azure fornisca SDK e servizi alternativi.
2. Se non si ha già una sottoscrizione di Azure, crearne una all'indirizzo [https://azure.com](https://azure.com).
3. Creare un account di Mappe di Azure ([documentazione](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) e una chiave di autenticazione per Azure Active Directory ([documentazione](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Eseguire la migrazione del codice dell'applicazione.
5. Testare l'applicazione dopo la migrazione.
6. Distribuire l'applicazione di cui è stata eseguita la migrazione in ambiente di produzione.

## <a name="azure-maps-technical-resources"></a>Risorse tecniche di Mappe di Azure

Ecco un elenco di risorse tecniche utili per Mappe di Azure.

- Panoramica: [https://azure.com/maps](https://azure.com/maps)
- Documentazione: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Esempi di codice di Web SDK: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Forum per sviluppatori: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Video: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Feedback su Mappe di Azure (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Supporto per la migrazione

Gli sviluppatori possono richiedere supporto per la migrazione nei [forum](https://aka.ms/AzureMapsForums) o tramite una delle numerose opzioni di supporto di Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate su come eseguire la migrazione dell'applicazione Google Maps, vedere questi articoli:

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un'app Web](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un'app Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un servizio Web](migrate-from-google-maps-web-services.md)
