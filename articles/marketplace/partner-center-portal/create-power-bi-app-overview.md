---
title: Panoramica della creazione di app Power BI-Azure Marketplace
description: Questo articolo descrive i passaggi di alto livello per la pubblicazione di un'app Power BI in Microsoft AppSource. Sono disponibili anche i requisiti tecnici e aziendali che l'app Power BI deve soddisfare per essere pubblicata nel Marketplace commerciale.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 8f050a43cbdf4ab29df55cd5526eb231c301b271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732669"
---
# <a name="power-bi-app-creation-overview"></a>Panoramica della creazione di app Power BI

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte di Power BI app da portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni riportate nell' [offerta Power bi app](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) per portale cloud partner per gestire le offerte.

Questo articolo illustra come pubblicare un'app Power BI in Microsoft [AppSource](https://appsource.microsoft.com/). Un Power BI i pacchetti dell'app contenuti personalizzabili, inclusi set di dati, report e dashboard. È quindi possibile usare l'app con altre piattaforme Power BI usando AppSource, eseguire le modifiche e le personalizzazioni consentite dallo sviluppatore e connetterle ai propri dati.

## <a name="publishing-benefits"></a>Vantaggi della pubblicazione

Vantaggi della pubblicazione nel Marketplace commerciale:

- Promuovere la propria azienda usando il marchio Microsoft.
- È potenzialmente raggiungibile da più di 100 milioni utenti di Office 365 e Dynamics 365 in AppSource e più di 200.000 organizzazioni tramite Azure Marketplace.
- Ricevi lead di alta qualità da questi Marketplace.
- I servizi promossi dai team Microsoft Field e Telesales.

## <a name="overview"></a>Panoramica

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Panoramica dei passaggi per la pubblicazione di un'app Power BI" border="false":::

Di seguito sono riportati i passaggi principali della pubblicazione:

1. Creare l'applicazione in Power BI. Si riceverà un collegamento per l'installazione del pacchetto, ovvero l'asset tecnico principale per l'offerta. Inviare il pacchetto di test alla pre-produzione prima di creare l'offerta nel centro per i partner. Per informazioni dettagliate, vedere [che cosa sono le app Power bi?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Aggiungere i materiali di marketing, ad esempio nome ufficiale, descrizione e logo.
3. Includere i documenti legali e di supporto dell'offerta, ad esempio le condizioni per l'utilizzo, l'informativa sulla privacy, i criteri di supporto e la guida dell'utente.
4. Creare l'offerta: usare il centro per i partner per modificare i dettagli, incluse la descrizione dell'offerta, i materiali di marketing, le informazioni legali, le informazioni di supporto e le specifiche degli asset.
5. Inviarlo per la pubblicazione.
6. Monitora il processo nel centro per i partner, dove il team di onboarding di AppSource testa, convalida e certifica l'app.
7. Dopo la certificazione, esaminare l'app nell'ambiente di test e rilasciarla. Questa operazione verrà elencata in AppSource ("passa in tempo reale").
8. In Power BI inviare il pacchetto in produzione. Per informazioni dettagliate, vedere [gestire la versione dell'app Power bi](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Prima di iniziare

Esaminare i collegamenti riportati di seguito, che forniscono modelli, suggerimenti ed esempi.

- [Creare un'app di Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Suggerimenti per la creazione di un'app Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Esempi](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Requisiti

Per la pubblicazione nel Marketplace commerciale, l'offerta Power BI app deve soddisfare i requisiti tecnici e aziendali seguenti.

### <a name="technical-requirements"></a>Requisiti tecnici

Il principale asset tecnico necessario è un' [app Power bi](https://go.microsoft.com/fwlink/?linkid=2028636). Si tratta di una raccolta di set di risultati primari, report o dashboard. Include anche servizi connessi facoltativi e set di dati incorporati, noti in precedenza come [pacchetto di contenuto](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Per altre informazioni sullo sviluppo di questo tipo di app, vedere [che cosa sono le app Power bi?](https://go.microsoft.com/fwlink/?linkid=2028636).

#### <a name="get-an-installation-web-address"></a>Ottenere un indirizzo Web di installazione

È possibile compilare un'app Power BI solo nell'ambiente [Power bi](https://powerbi.microsoft.com/) .

1. Accedere con una [licenza di Power bi Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Creare e testare l'app in Power BI.
3. Quando si riceve l'indirizzo Web di installazione dell'app, aggiungerlo alla pagina **configurazione tecnica** nel centro per i partner.

Dopo aver creato e testato l'app in Power BI, salvare l'indirizzo Web di installazione dell'applicazione, perché sarà necessario per [creare un'offerta di Power bi app](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer).

### <a name="business-requirements"></a>Requisiti aziendali

I requisiti aziendali includono le procedure, i contratti e gli obblighi legali. È necessario:

- Essere un editore di Marketplace commerciale registrato. Se non si è registrati, seguire i passaggi in [diventare un editore del Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/become-publisher).
- Fornire contenuti che soddisfino i criteri per l'offerta da elencare in AppSource. Per altre informazioni, vedere [avere un'app da elencare in AppSource? Ecco come](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Accettare e seguire l' [informativa sulla privacy Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'offerta di app Power BI nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)