---
title: Cloud Solution Provider - Marketplace commerciale Microsoft
description: Informazioni su come vendere le offerte tramite il canale dei partner del programma Microsoft Cloud Solution Provider (CSP) nel marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131174"
---
# <a name="cloud-solution-provider-program"></a>Programma per provider di soluzioni cloud

Questo articolo illustra come configurare l'offerta in modo che sia disponibile per il programma Cloud Solution Provider (CSP). Oltre alla pubblicazione delle offerte tramite i [negozi online del Marketplace commerciale](overview.md#commercial-marketplace-online-stores), è anche possibile vendere tramite il programma CSP per raggiungere milioni di clienti Microsoft qualificati che il programma serve.

È possibile configurare offerte nuove o esistenti per la disponibilità nel programma CSP su base esplicita, in modo da consentire ai partner CSP di vendere i prodotti e creare soluzioni in bundle per i clienti.

Gli editori sono tenuti a fornire il supporto in garanzia ai clienti finali e a fornire un meccanismo per essere contattati dai partner del programma CSP e/o dai clienti per richiedere supporto. È consigliabile fornire ai partner del programma CSP la documentazione per gli utenti, il training e le notifiche sull'integrità/interruzione dei servizi (se applicabile), in modo che i partner del programma CSP siano in grado di gestire le richieste di supporto di livello 1 da parte dei clienti.  

Le offerte seguenti sono idonee per il consenso esplicito alla vendita da parte dei partner del programma CSP:

- Offerte di transazione Software-as-a-Service (SaaS)
- Macchine virtuali
- Modelli di soluzioni
- Applicazioni gestite

> [!NOTE]
> Per impostazione predefinita, i contenitori e i piani di VM Bring your own License (BYOL) vengono scelti per essere venduti dai partner del programma CSP.

## <a name="how-to-configure-an-offer"></a>Come configurare un'offerta

Configurare l'impostazione di consenso esplicito del programma CSP quando si crea l'offerta nel centro per i partner. [Altre informazioni sulla modifica dell'esperienza di pubblicazione](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Consenso esplicito del Centro per i partner

L'esperienza di consenso esplicito si trova nel modulo del pubblico del rivenditore CSP:

![Destinatari rivenditori CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Scegliere tra tre opzioni:

1. Qualsiasi partner nel programma CSP.
2. Partner specifici nel programma CSP selezionato.
3. Nessun partner nel programma CSP.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Opzione 1: qualsiasi partner nel programma CSP

![Qualsiasi partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Se si sceglie questa opzione, tutti i partner del programma CSP saranno idonei a rivendere l'offerta ai clienti.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Opzione 2: partner specifici nel programma CSP selezionato

![Partner specifici nel programma CSP selezionato](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Se si sceglie questa opzione, si autorizzano alcuni partner del programma CSP come idonei a rivendere l'offerta.

Per autorizzare i partner, selezionare **Seleziona partner CSP** . verrà visualizzato un menu che consente di eseguire la ricerca in base al nome del partner o all'ID del tenant Azure Active Directory (Azure ad) CSP.

![Menu di selezione CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

È possibile applicare filtri di ricerca, ad esempio **Paese** , **Competenza** o **Competenze** .

![Filtri relativi a paese/area geografica e competenze per la ricerca di partner](media/marketplace-publishers-guide/csp-add-resellers.png)

Dopo aver scelto l'elenco dei partner, selezionare **Aggiungi** .

![Elenco di esempio di partner autorizzati nel programma CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Una tabella con l'elenco dei partner selezionati viene visualizzata nella pagina Destinatari rivenditori CSP.

![Tabella con elenco di partner nella Destinatari rivenditori CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selezionare **Salva bozza** per registrare le modifiche.

Se questa offerta non è stata pubblicata, sarà necessario pubblicarla per renderla disponibile ai partner selezionati.

>[!NOTE]
>Se un partner del programma CSP in una determinata area viene autorizzato, può vendere l'offerta a qualsiasi cliente appartenente a tale area specifica. Per altre informazioni su come le offerte CSP sono classificate in aree, vedere la pagina relativa ai [mercati e alla valuta per il programma Cloud Solution Provider](/partner-center/regional-authorization-overview).

Se si sta aggiornando l'elenco CSP di un'offerta già pubblicata, aggiungere gli altri partner e selezionare **Sync CSP audience** (Sincronizza destinatari CSP).

Se si ha un'offerta che comprende già un elenco di partner autorizzati e si vuole usare lo stesso elenco per un'altra offerta, usare **Importazione/Esportazione** . Passare all'offerta con l'elenco CSP e selezionare **Esporta CSP** . La funzione sviluppa un file CSV che può essere importato in un'altra offerta.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Opzione 3: nessun partner nel programma CSP

![Nessun partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Scegliendo questa opzione, si rifiuta esplicitamente l'offerta per il programma CSP. È possibile modificare questa selezione in qualsiasi momento.

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

Se l'offerta attuale è **Opzione 2: Partner specifici nel programma CSP selezionato** e si vuole passare a **Opzione uno: Qualsiasi partner nel programma CSP** , seguire questa procedura per creare una richiesta:

1. Andare alla [pagina della richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni dei menu a discesa prepopolati.

2. Per **Select the product version** (Seleziona la versione del prodotto) selezionare **Live offer management** (Gestione offerta live).
3. Per **Select a category that best describe the issue** (Seleziona una categoria che descrive meglio il problema) scegliere la categoria che fa riferimento all'offerta.
4. Per **Select a problem that best describes the issue** (Seleziona un problema che descrive meglio l'errore) selezionare **Update existing offer** (Aggiorna offerta esistente).
5. Selezionare **Avanti** per passare alla pagina **Dettagli del problema** dove è possibile immettere altri dettagli sul problema.
6. Usare **Deauthorize CSP** come titolo del problema e compilare la altre sezioni obbligatorie.

 Se l'offerta attuale è **Opzione 2: Partner specifici nel programma CSP selezionato** e si vuole passare a **Opzione 3: Nessun partner nel programma CSP** , sarà possibile passare a tale opzione solo se i partner del programma CSP autorizzati in precedenza non hanno rivenduto l'offerta ai clienti finali. Usare le istruzioni seguenti per creare una richiesta:

1. Andare alla [pagina della richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni dei menu a discesa prepopolati.

2. Per **Select the product version** (Seleziona la versione del prodotto) selezionare **Live offer management** (Gestione offerta live).
3. Per **Select a category that best describe the issue** (Seleziona una categoria che descrive meglio il problema) scegliere la categoria che fa riferimento all'offerta.
4. Per **Select a problem that best describes the issue** (Seleziona un problema che descrive meglio l'errore) selezionare **Update existing offer** (Aggiorna offerta esistente).
5. Selezionare **Avanti** per passare alla pagina **Dettagli del problema** dove è possibile immettere altri dettagli sul problema.
6. Usare **Deauthorize CSP** come titolo del problema e compilare la altre sezioni obbligatorie.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Uscire dall'opzione 3: Nessun partner nel programma CSP

Se l'offerta attuale è **Opzione 3: Nessun partner nel programma CSP** , è possibile passare a una delle altre due opzioni in qualsiasi momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Condivisione di materiali di vendita e supporto con i partner del programma CSP

Per consentire ai partner del programma Cloud Solution Provider di rappresentare in modo più efficace l'offerta e di interagire con l'organizzazione, è necessario inviare i materiali di vendita e supporto che saranno disponibili per i rivenditori. Queste risorse non verranno esposte ai clienti negli archivi online.

### <a name="partner-center-csp-channel"></a>Canale CSP del Centro per i partner

Se si è scelto il canale CSP nel centro per i partner, gli editori devono immettere un URL che ospita i materiali di marketing pertinenti e le informazioni di contatto del canale nel modulo di presentazione dell'offerta.

![Informazioni aggiuntive sui partner CSP nel Centro per i partner](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sui [Servizi go-to-Market](https://partner.microsoft.com/reach-customers/gtm).
- Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare e configurare l'offerta.