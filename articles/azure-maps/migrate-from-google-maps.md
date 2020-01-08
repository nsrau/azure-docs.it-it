---
title: Eseguire la migrazione da Google Maps | Microsoft Docs
description: Esercitazione su come eseguire la migrazione da Google Maps a mappe di Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ea9afc413a7fb187042c9d832fa1c16802993eed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481230"
---
# <a name="migrate-from-google-maps"></a>Eseguire la migrazione da Google Maps

Questa esercitazione fornisce informazioni dettagliate su come eseguire la migrazione di applicazioni Web, per dispositivi mobili e basate su server da Google Maps alla piattaforma Microsoft Azure maps. Questa esercitazione include esempi di codice comparato, suggerimenti per la migrazione e procedure consigliate per la migrazione a mappe di Azure.

## <a name="azure-maps-platform-overview"></a>Panoramica della piattaforma mappe di Azure

Azure Maps offre agli sviluppatori di tutti i settori potenti funzionalità geospaziali, con i dati della mappa aggiornati regolarmente per fornire contesto geografico per le applicazioni Web e per dispositivi mobili. Mappe di Azure dispone di un set di API REST conforme all'API di Azure per le mappe, la ricerca, il routing, il traffico, i fusi orari, la georilevazione, il geoschermatura, i dati delle mappe e le operazioni spaziali, unitamente agli SDK Web e Android, per rendere lo sviluppo semplice, flessibile e portatile tra più piattaforme.

## <a name="high-level-platform-comparison"></a>Confronto tra piattaforme di alto livello

La tabella seguente include un elenco generale delle funzionalità di Google Maps e il relativo supporto per le funzionalità di Azure maps. Questo elenco non include le funzionalità aggiuntive di Azure Maps, ad esempio l'accessibilità, le API di geoschermatura, isocrone, le operazioni spaziali, l'accesso diretto alle tessere, i servizi batch e i confronti di code coverage (ad esempio, la copertura delle immagini).

| Funzionalità di Google Maps         | Supporto per mappe di Azure                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Pianificata                                |
| API del servizio REST           | ✓                                      |
| Direzioni (routing)        | ✓                                      |
| Matrice di distanza             | ✓                                      |
| Altitudine                   | Pianificata                                |
| Geocodifica (diretta/inversa) | ✓                                      |
| Georilevazione                 | N/D                                    |
| Cerca in posizioni               | ✓                                      |
| Dettagli località              | N/A: sito Web & numero di telefono disponibile |
| Inserisce le foto               | N/D                                    |
| Inserisci completamento automatico          | ✓                                      |
| Mappe statiche                 | ✓                                      |
| Visualizzazione statica via          | N/D                                    |
| Fuso orario                   | ✓                                      |
| API Embedded Maps           | N/D                                    |
| URL mappa                    | N/D                                    |

Google Maps fornisce l'autenticazione basata su chiavi di base. Azure Maps fornisce l'autenticazione basata su chiavi di base, oltre a una sicurezza elevata Azure Active Directory.

## <a name="licensing-considerations"></a>Considerazioni sulle licenze

Quando si esegue la migrazione ad Azure Maps da Google Maps, è necessario considerare i punti seguenti per quanto riguarda le licenze.

- Azure Maps addebita l'utilizzo di mappe interattive in base al numero di tessere mappa caricate, mentre Google Maps addebita il caricamento del controllo mappa. Negli SDK di Maps di Azure interattivo, le tessere mappa vengono automaticamente memorizzate nella cache per ridurre i costi per lo sviluppatore. Viene generata una transazione di Azure Maps per ogni 15 riquadri mappa caricati. Gli SDK di Azure Maps interattivo usano i riquadri di 512 pixel e in media generano una o meno transazioni per ogni visualizzazione pagina.
- Spesso è molto più conveniente sostituire le immagini della mappa statica dai servizi Web di Google Maps con Azure Maps Web SDK, perché questo usa i riquadri della mappa e, a meno che l'utente non riquadri e ingrandisca la mappa, genererà spesso solo una frazione di una transazione per ogni carico di mappa. Azure Maps Web SDK include opzioni per disabilitare la panoramica e lo zoom. Inoltre, Azure Maps Web SDK offre molto più opzioni di visualizzazione rispetto a un servizio Web mappa statica.
- Mappe di Azure consente di archiviare i dati dalla propria piattaforma in Azure. Può anche essere memorizzato nella cache altrove per un massimo di sei mesi in base alle condizioni per l' [utilizzo](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Di seguito sono riportate alcune risorse correlate per le mappe di Azure:

- [Pagina dei prezzi di mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- Termini [di utilizzo di mappe di Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluse nelle condizioni di Microsoft Online Services)
- [Scegliere il piano tariffario corretto in mappe di Azure](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Piano di migrazione suggerito

Di seguito è riportato un piano di migrazione di alto livello.

1. Eseguire l'inventario degli SDK di Google Maps e dei servizi usati dall'applicazione e verificare che Azure Maps fornisca SDK e servizi alternativi per la migrazione a.
2. Creare una sottoscrizione di Azure (se non ne è già presente una) in [https://azure.com](https://azure.com).
3. Creare un account Azure Maps ([documentazione](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) e una chiave di autenticazione o Azure Active Directory ([documentazione](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Eseguire la migrazione del codice dell'applicazione.
5. Testare l'applicazione migrata.
6. Distribuire l'applicazione migrata in produzione.

## <a name="azure-maps-technical-resources"></a>Risorse tecniche di Azure Maps

Ecco un elenco di risorse tecniche utili per le mappe di Azure.

- Panoramica: [https://azure.com/maps](https://azure.com/maps)
- Documentazione: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Esempi di codice dell'SDK Web: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Forum per sviluppatori: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Video: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Commenti e suggerimenti su Azure Maps (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Supporto della migrazione

Gli sviluppatori possono richiedere supporto per la migrazione tramite i [Forum](https://aka.ms/AzureMapsForums) o tramite una delle numerose opzioni di supporto per Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire la migrazione dell'applicazione Google Maps con questi articoli:

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un'app Web](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un'app Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un servizio Web](migrate-from-google-maps-web-services.md)
