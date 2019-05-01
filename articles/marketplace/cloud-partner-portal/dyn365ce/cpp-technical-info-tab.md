---
title: Dynamics 365 per la scheda delle informazioni tecniche Engagement dei clienti | Azure Marketplace
description: Come specificare le informazioni tecniche per un'applicazione Dynamics 365 for Customer Engagement nel marketplace AppSource.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: df7f3be5a92a183176da7851ce1943793b8b57d0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942368"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Scheda Technical Info (Informazioni tecniche) di Dynamics 365 for Customer Engagement

La scheda **Technical Info** Technical Info (Informazioni tecniche) della pagina **New Offer** (Nuova offerta) consente di specificare informazioni dettagliate sull'applicazione Dynamics 365 for Customer Engagement, tra cui pacchetto CRM e logo di marketing.  Questa scheda è suddivisa in quattro sezioni: **Application Info** (Informazioni sull'applicazione), **CRM Package** (Pacchetto CRM), **CRM Package Availability** (Disponibilità pacchetto CRM) e **Marketing Artifacts** (Artefatti di marketing). Un asterisco (*) alla fine del nome del campo indica che il campo è obbligatorio. 


## <a name="application-info-section"></a>Sezione Application Info (Informazioni sull'applicazione)

In questa sezione si forniscono i dettagli sull'applicazione Dynamics 365.

![Sezione di informazioni sull'applicazione della scheda di informazioni tecniche](./media/dynce-technical-info-tab1.png)

La tabella seguente descrive i campi. I campi obbligatori sono indicati da un asterisco (*).

|      Campo                    |    DESCRIZIONE                  |
|    ---------                  |  ---------------                |
|   Modello di licenza di base\*          |  Il modello di licenza determina in che modo l'applicazione viene assegnata ai clienti nell'interfaccia di amministrazione di Dynamics 365. Le licenze per **risorsa** sono basate su istanza, mentre le licenze **utente** vengono assegnate una per ogni tenant.  |
|  Accesso a CRM Secure Store & S2S in uscita\* |  Abilita la configurazione dell'accesso all'archiviazione sicura di CRM e dell'accesso in uscita S2S (Server-to-Server). *Questa funzionalità richiede una considerazione speciale da parte del Team di Dynamics 365 durante la fase di certificazione.* Microsoft contatterà l'utente per completare alcuni passaggi aggiuntivi per supportare questa funzionalità.  |
| Sottoscrivere gli eventi del ciclo di vita CRM\* | Per l'integrazione con gli eventi del ciclo di vita di Dynamics 365 è necessario fornire un servizio dedicato, registrato tramite un contratto speciale con Microsoft. *Questa funzionalità richiede una considerazione speciale da parte del Team di Dynamics 365 durante la fase di certificazione.* Microsoft contatterà l'utente per completare alcuni passaggi aggiuntivi per supportare questa funzionalità.  |
| Application Configuration URL (URL configurazione applicazione) | URL della pagina Web che consente all'utente di configurare l'applicazione |
| Applicable Dynamics 365 products (Prodotti Dynamics 365 applicabili)  | Selezionare i prodotti Dynamics 365 per cui è valida l'offerta. Questa offerta verrà visualizzata tra i prodotti selezionati in AppSource.  |
| Marketing Only Change (Solo modifica di marketing)         | Impostando questa opzione su Yes (Sì) si indica che sono state apportate solo modifiche descrittive o di marketing all'offerta esistente.  Le modifiche di questo tipo consentono all'offerta di ignorare le fasi di certificazione e provisioning.  |
|  |  |


## <a name="crm-package-section"></a>Sezione CRM package (Pacchetto CRM)

In questa sezione si forniscono i dettagli sul file del pacchetto AppSource.  Queste informazioni verranno usate dai team di convalida e certificazione di Dynamics 365.

![Sezione sul pacchetto CRM della scheda di informazioni tecniche](./media/dynce-technical-info-tab2.png)

La tabella seguente descrive i campi.  I campi obbligatori sono indicati da un asterisco (*).

|      Campo                    |    DESCRIZIONE                  |
|    ---------                  |  ---------------                |
|  Nome file del pacchetto\*     |  Nome file del pacchetto (ZIP).  Questo nome *non* è pubblico e verrà usato internamente dal team di certificazione di Dynamics 365.  |
|  URL del percorso del pacchetto\*      |  URL di un account di archiviazione di Azure che contiene il file del pacchetto caricato. Questo URL deve includere una chiave di firma di accesso condiviso di sola lettura per consentire al team di prelevare il pacchetto per la verifica.  |
| Più di un pacchetto di crm\*     | Selezionare Yes (Sì) SOLO se si supportano più versioni di CRM con pacchetti diversi.  Ogni versione avrà un file del pacchetto corrispondente che è necessario creare singolarmente.  |
| Scenario e usare asset di case\*   | Abilita il caricamento di un documento di specifica funzionale per l'applicazione, che verrà usato dal team di convalida di Dynamics 365.  Il formato preferito per questa specifica è il [modello di scenario utente E2E](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>Sezione CRM Package Availability (Disponibilità pacchetto CRM)

In questa sezione, selezionare le aree geografiche in cui l'applicazione sarà disponibile per i clienti.  La distribuzione nelle aree sovrane seguenti *richiede un'autorizzazione speciale e la convalida* durante il processo di certificazione: [Germania](https://docs.microsoft.com/azure/germany/), [Cloud del governo degli Stati Uniti](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) e TIP.


## <a name="marketing-artifacts-section"></a>Sezione Marketing Artifacts (Artefatti di marketing)

In questa sezione è necessario caricare un logo dell'applicazione che verrà usato per rappresentare il pacchetto nel marketplace AppSource.  L'immagine del logo deve essere in formato PNG e di dimensioni pari a 255x115 pixel.


## <a name="next-steps"></a>Passaggi successivi

È consigliabile offrire una dimostrazione dell'applicazione completando la [scheda Test Drive](./cpp-testdrive-tab.md)
