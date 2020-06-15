---
title: Cloud Solution Provider - Marketplace commerciale Microsoft
description: Informazioni su come vendere le offerte tramite il canale dei partner del programma Microsoft Cloud Solution Provider (CSP) nel marketplace commerciale.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: 58bfe5cdc58c41c2ead82ac2d280629e4fec9bcd
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798140"
---
# <a name="cloud-solution-provider-program"></a>Programma per provider di soluzioni cloud

Questo articolo illustra come configurare l'offerta in modo che sia disponibile per il programma Cloud Solution Provider (CSP). Oltre a pubblicare le offerte tramite le [vetrine Web del marketplace commerciale](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace), è anche possibile venderle tramite il programma CSP per raggiungere milioni di clienti Microsoft qualificati gestiti dal programma.

È possibile configurare offerte nuove o esistenti in modo che siano disponibili nel programma CSP per il consenso esplicito. Ciò consente ai partner CSP di vendere i prodotti e creare soluzioni in bundle per i clienti.

Gli editori sono tenuti a fornire il supporto in garanzia ai clienti finali e a fornire un meccanismo per essere contattati dai partner del programma CSP e/o dai clienti per richiedere supporto. È consigliabile fornire ai partner del programma CSP la documentazione per gli utenti, il training e le notifiche sull'integrità/interruzione dei servizi (se applicabile), in modo che i partner del programma CSP siano in grado di gestire le richieste di supporto di livello 1 da parte dei clienti.  

Le offerte seguenti sono idonee per il consenso esplicito alla vendita da parte dei partner del programma CSP:

- Offerte di transazione Software-as-a-Service (SaaS)
- Macchine virtuali
- Modelli di soluzioni
- Applicazioni gestite

> [!NOTE]
> Per impostazione predefinita, i partner del programma CSP acconsentono esplicitamente alla vendita di contenitori e SKU di macchine virtuali BYOL (Bring Your Own License).

## <a name="how-to-configure-an-offering"></a>Come configurare un'offerta

L'impostazione del consenso esplicito del programma CSP viene configurata nell'esperienza di creazione dell'offerta del portale Cloud Partner o del Centro per i partner. [Altre informazioni sulla modifica dell'esperienza di pubblicazione](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Consenso esplicito del Centro per i partner

Nel Centro per i partner l'esperienza di consenso esplicito è disponibile nel modulo Destinatari rivenditori CSP.

![Destinatari rivenditori CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Nel modulo Destinatari rivenditori CSP è possibile scegliere tra tre opzioni:

- Opzione uno: Qualsiasi partner nel programma CSP
- Opzione due: Partner specifici nel programma CSP selezionato
- Opzione tre: Nessun partner nel programma CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opzione uno: Qualsiasi partner nel programma CSP

![Qualsiasi partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Se si sceglie questa opzione, tutti i partner del programma CSP saranno idonei a rivendere l'offerta ai clienti.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opzione due: Partner specifici nel programma CSP selezionato

![Partner specifici nel programma CSP selezionato](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Se si sceglie questa opzione, si autorizzano alcuni partner del programma CSP come idonei a rivendere l'offerta.

Per autorizzare i partner, fare clic su **Seleziona partner CSP**. Verrà visualizzato un menu che consente di eseguire una ricerca per nome partner o ID tenant di Azure Active Directory (AAD).

![Menu di selezione CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

È possibile applicare filtri di ricerca, ad esempio **Paese**, **Competenza** o **Competenze**.

![Filtri relativi a paese/area geografica e competenze per la ricerca di partner](media/marketplace-publishers-guide/csp-add-resellers.png)

Dopo aver scelto l'elenco dei partner, selezionare **Aggiungi**.

![Elenco di esempio di partner autorizzati nel programma CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Una tabella con l'elenco dei partner selezionati viene visualizzata nella pagina Destinatari rivenditori CSP.

![Tabella con elenco di partner nella Destinatari rivenditori CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selezionare **Salva bozza** per registrare le modifiche.

Se questa offerta non è stata pubblicata, sarà necessario pubblicarla per renderla disponibile ai partner selezionati.

>[!NOTE]
>Se un partner del programma CSP in una determinata area viene autorizzato, può vendere l'offerta a qualsiasi cliente appartenente a tale area specifica. Per altre informazioni su come le offerte CSP sono classificate nelle diverse aree, vedere [Valute e mercati a livello di area del programma Cloud Solution Provider](https://docs.microsoft.com/partner-center/regional-authorization-overview).

Se si sta aggiornando l'elenco CSP di un'offerta già pubblicata, aggiungere gli altri partner e selezionare **Sync CSP audience** (Sincronizza destinatari CSP).

Se si ha un'offerta che comprende già un elenco di partner autorizzati e si vuole usare lo stesso elenco per un'altra offerta, usare **Importazione/Esportazione**. Passare all'offerta con l'elenco CSP e selezionare **Esporta CSP**. La funzione sviluppa un file CSV che può essere importato in un'altra offerta.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opzione tre: Nessun partner nel programma CSP

![Nessun partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Scegliendo questa opzione, si rifiuta esplicitamente l'offerta per il programma CSP. È possibile modificare questa selezione in qualsiasi momento.

### <a name="cloud-partner-portal-opt-in"></a>Consenso esplicito nel portale Cloud Partner

Nel portale Cloud Partner il consenso esplicito viene impostato nella scheda Marketplace o Vetrina. È possibile scegliere partner specifici nel programma CSP solo nel Centro per i partner.

![Esperienza di consenso esplicito CSP nel Centro per i partner](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Annullare l'autorizzazione per i partner nel programma CSP

Se si è autorizzato un partner del programma CSP che ha già rivenduto il prodotto ai propri clienti, non sarà possibile annullare l'autorizzazione per tale partner.

Se un partner del programma CSP non ha venduto il prodotto ai clienti e si vuole rimuovere il partner CSP dopo la pubblicazione dell'offerta, seguire questa procedura:

1. Andare alla [pagina della richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni dei menu a discesa prepopolati.

2. Per **Select the product version** (Seleziona la versione del prodotto) selezionare **Live offer management** (Gestione offerta live).
3. Per **Select a category that best describe the issue** (Seleziona una categoria che descrive meglio il problema) scegliere la categoria che fa riferimento all'offerta.
4. Per **Select a problem that best describes the issue** (Seleziona un problema che descrive meglio l'errore) selezionare **Update existing offer** (Aggiorna offerta esistente).
5. Selezionare **Avanti** per passare alla pagina **Dettagli del problema** dove è possibile immettere altri dettagli sul problema.
6. Usare **Deauthorize CSP** come titolo del problema e compilare la altre sezioni obbligatorie.




## <a name="navigate-between-options"></a>Passare da un'opzione all'altra

Questa sezione illustra come passare da una delle tre opzioni per i rivenditori CSP a un'altra.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Uscire dall'opzione uno: Qualsiasi partner nel programma CSP

Se l'offerta attuale è **Opzione 1: Qualsiasi partner nel programma CSP** e si vuole passare a una delle altre due opzioni, seguire questa procedura per creare una richiesta:

1. Andare alla [pagina della richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni dei menu a discesa prepopolati.

2. Per **Select the product version** (Seleziona la versione del prodotto) selezionare **Live offer management** (Gestione offerta live).
3. Per **Select a category that best describe the issue** (Seleziona una categoria che descrive meglio il problema) scegliere la categoria che fa riferimento all'offerta.
4. Per **Select a problem that best describes the issue** (Seleziona un problema che descrive meglio l'errore) selezionare **Update existing offer** (Aggiorna offerta esistente).
5. Selezionare **Avanti** per passare alla pagina **Dettagli del problema** dove è possibile immettere altri dettagli sul problema.
6. Usare **Deauthorize CSP** come titolo del problema e compilare la altre sezioni obbligatorie.

> [!NOTE]
> Se si sta provando a passare all'opzione due e un partner del programma CSP ha già rivenduto l'offerta ai propri clienti, tale partner è già nell'elenco di partner autorizzati per impostazione predefinita.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Uscire dall'opzione due: Partner specifici nel programma CSP selezionato

Se l'offerta attuale è **Opzione 2: Partner specifici nel programma CSP selezionato** e si vuole passare a **Opzione uno: Qualsiasi partner nel programma CSP**, seguire questa procedura per creare una richiesta:

1. Andare alla [pagina della richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni dei menu a discesa prepopolati.

2. Per **Select the product version** (Seleziona la versione del prodotto) selezionare **Live offer management** (Gestione offerta live).
3. Per **Select a category that best describe the issue** (Seleziona una categoria che descrive meglio il problema) scegliere la categoria che fa riferimento all'offerta.
4. Per **Select a problem that best describes the issue** (Seleziona un problema che descrive meglio l'errore) selezionare **Update existing offer** (Aggiorna offerta esistente).
5. Selezionare **Avanti** per passare alla pagina **Dettagli del problema** dove è possibile immettere altri dettagli sul problema.
6. Usare **Deauthorize CSP** come titolo del problema e compilare la altre sezioni obbligatorie.

 Se l'offerta attuale è **Opzione 2: Partner specifici nel programma CSP selezionato** e si vuole passare a **Opzione 3: Nessun partner nel programma CSP**, sarà possibile passare a tale opzione solo se i partner del programma CSP autorizzati in precedenza non hanno rivenduto l'offerta ai clienti finali. Seguire questa procedura per creare una richiesta:

1. Andare alla [pagina della richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni dei menu a discesa prepopolati.

2. Per **Select the product version** (Seleziona la versione del prodotto) selezionare **Live offer management** (Gestione offerta live).
3. Per **Select a category that best describe the issue** (Seleziona una categoria che descrive meglio il problema) scegliere la categoria che fa riferimento all'offerta.
4. Per **Select a problem that best describes the issue** (Seleziona un problema che descrive meglio l'errore) selezionare **Update existing offer** (Aggiorna offerta esistente).
5. Selezionare **Avanti** per passare alla pagina **Dettagli del problema** dove è possibile immettere altri dettagli sul problema.
6. Usare **Deauthorize CSP** come titolo del problema e compilare la altre sezioni obbligatorie.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Uscire dall'opzione 3: Nessun partner nel programma CSP

Se l'offerta attuale è **Opzione 3: Nessun partner nel programma CSP**, è possibile passare a una delle altre due opzioni in qualsiasi momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Condivisione di materiali di vendita e supporto con i partner del programma CSP

Per consentire ai partner del programma Cloud Solution Provider di rappresentare nel modo più efficace l'offerta e di interagire con l'organizzazione, è necessario inviare i materiali di vendita e supporto che saranno disponibili per i rivenditori. Queste risorse non verranno esposte ai clienti nelle vetrine del marketplace.

### <a name="partner-center-csp-channel"></a>Canale CSP del Centro per i partner

Se si è dato il consenso esplicitamente per il canale CSP nel Centro per i partner, gli editori devono immettere un URL che ospita i materiali di marketing pertinenti e le informazioni di contatto del canale nel modulo di inserzione dell'offerta del canale CSP:

![Informazioni aggiuntive sui partner CSP nel Centro per i partner](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Canale CSP nel portale Cloud Partner

Se si è dato il consenso esplicitamente per il canale CSP nel portale Cloud Partner, gli editori devono immettere un URL che ospita i materiali di marketing pertinenti e le informazioni di contatto del canale nel canale CSP:

![Informazioni aggiuntive sui partner CSP nel portale Cloud Partner](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Passaggi successivi

Visitare la [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Per altre informazioni sui servizi per l'immissione sul mercato del marketplace, vedere [Servizi per l'immissione sul mercato](https://partner.microsoft.com/reach-customers/gtm).

Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare e configurare l'offerta.
