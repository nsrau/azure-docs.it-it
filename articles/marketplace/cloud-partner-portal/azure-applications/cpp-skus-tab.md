---
title: Configurare gli SKU per un'offerta di applicazioni Azure | Microsoft Docs
description: Come configurare gli SKU per un'applicazione gestita di Azure e un modello di soluzione di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5d6ec0197699f603c79f414e015cdebcde6b9f60
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905614"
---
# <a name="azure-application-skus-tab"></a>Scheda SKU per un'applicazione Azure

Questo articolo descrive come usare la scheda SKU per creare gli SKU per l'applicazione Azure. 

> [!IMPORTANT]
> La procedura per la configurazione di uno SKU è diversa per un'offerta di applicazione gestita e un'offerta di modello di soluzione. Queste differenze sono documentate in questo articolo. 

## <a name="configure-azure-application-skus"></a>Configurare gli SKU per l'applicazione Azure

### <a name="create-a-new-sku"></a>Creare un nuovo SKU

Seguire questa procedura per creare un nuovo SKU:

1. Selezionare la scheda **SKU**.
2. In SKU selezionare **+ Nuovo SKU**.

    ![Richiesta nuovo SKU](./media/azureapp-plus-sku.png)

3. Nella finestra popup Nuovo SKU digitare un **ID SKU**. Questo ID non può superare i 50 caratteri e deve contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. L'ID SKU non può terminare con un trattino.
4. L'ID SKU è visibile ai clienti negli URL dei prodotti, nei modelli di Resource Manager (se applicabile) e nei report di fatturazione. Dopo aver pubblicato l'offerta, non sarà possibile modificare questo ID.

### <a name="sku-details-for-a-solution-template"></a>Dettagli dello SKU per un modello di soluzione

Specificare le impostazioni dello SKU seguenti:

- **Titolo**: il titolo dello SKU. Questo titolo viene visualizzato nella raccolta di questo elemento.
- **Riepilogo**: una breve descrizione di riepilogo dello SKU. (La lunghezza massima consentita è di 100 caratteri.)
- **Descrizione**: descrizione dettagliata dello SKU.
- **Tipo SKU**: elenco a discesa con i valori seguenti: "Modello di soluzione" e "Applicazione gestita". Per questo scenario, selezionare **Modello di soluzione**.
- **Disponibilità del Cloud**: la posizione dello SKU. Il valore predefinito è **Azure pubblico**.
Azure pubblico: questa macchina virtuale potrà essere distribuita ai clienti in tutte le aree di Azure pubbliche dotate di integrazione con il Marketplace.
- **Cloud di Azure per enti pubblici**: questa macchina virtuale verrà distribuita nel cloud di Azure per enti pubblici. Prima della pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft consiglia agli editori di testare la propria soluzione e verificare che funzioni come previsto nell'ambiente in uso. Per lo staging e il test, richiedere un [account di prova](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >Microsoft Azure per enti pubblici è una soluzione cloud della community di enti pubblici con accesso controllato per i clienti degli enti federali, statali e locali degli Stati Uniti E dei partner che forniscono servizi a tali enti.

- **Is this a private SKU?** (SKU privato?) : selezionare Sì se questo SKU è disponibile solo per un gruppo selezionato di clienti.

    ![Modulo Dettagli SKU per un modello di soluzione](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>Dettagli dello SKU per un'applicazione gestita

La schermata successiva mostra il modulo Dettagli SKU per un'applicazione gestita.

   ![Modulo Dettagli SKU per un'applicazione gestita](./media/azureapp-sku-details-managedapplication.png)

Configurare le impostazioni dello SKU seguenti:

- **Titolo**: il titolo dello SKU. Questo titolo viene visualizzato nella raccolta di questo elemento.
- **Riepilogo**: una breve descrizione di riepilogo dello SKU. (La lunghezza massima consentita è di 100 caratteri.)
- **Descrizione**: descrizione dettagliata dello SKU.
- **Tipo SKU**: elenco a discesa con i valori seguenti: "Modello di soluzione" e "Applicazione gestita". Per questo scenario, selezionare **Applicazione gestita**.
- **Disponibilità del Cloud**: la posizione dello SKU. Il valore predefinito è **Azure pubblico**.
- **Azure pubblico**: questa macchina virtuale potrà essere distribuita ai clienti in tutte le aree di Azure pubbliche dotate di integrazione con il Marketplace.
- **Cloud di Azure per enti pubblici**: questa macchina virtuale verrà distribuita nel cloud di Azure per enti pubblici. Prima della pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft consiglia agli editori di testare la propria soluzione e verificare che funzioni come previsto nell'ambiente in uso. Per lo staging e il test, richiedere un [account di prova](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >Microsoft Azure per enti pubblici è una soluzione cloud della community di enti pubblici con accesso controllato per i clienti degli enti federali, statali e locali degli Stati Uniti E dei partner che forniscono servizi a tali enti.

- **Is this a private SKU?** (SKU privato?) : selezionare Sì se questo SKU è disponibile solo per un gruppo selezionato di clienti.
- **Disponibilità per paese/area geografica**: usare **Seleziona aree** per visualizzare l'elenco di paesi/aree geografiche disponibili. Selezionare ogni paese/area geografica e quindi scegliere **OK** per salvare le selezioni effettuate. 

   ![Elenco di disponibilità per paese/area geografica](./media/azure-app-select-country-region.png)

- **Old Pricing** (Prezzo precedente): immettere il prezzo per lo SKU, in USD al mese. I prezzi sono impostati nella valuta locale in base ai tassi di cambio correnti al momento della configurazione. Si consiglia di verificarli trattandosi di impostazioni personali. Per impostare o visualizzare il prezzo di ogni paese/area geografica singolarmente, esportare il foglio di calcolo dei prezzi e importarlo con i prezzi personalizzati.

  >[!NOTE]
  >Salvare le modifiche ai prezzi per abilitare l'esportazione/importazione dei dati relativi ai prezzi.

- **Prezzi in valuta semplificati**: immettere il prezzo per lo SKU, in USD al mese. Deve corrispondere al prezzo precedente. Per altre informazioni, vedere [Prezzi di valuta semplificata](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer).

### <a name="package-details-for-solution-template"></a>Dettagli del pacchetto per un modello di soluzione

Specificare i dettagli del pacchetto seguenti:

- **Versione**: la versione del pacchetto che verrà caricato. I tag di versione devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi.
- **File pacchetto (ZIP)**: pacchetto che contiene i file seguenti, salvati in un file ZIP.
  - applianceMainTemplate.json: file del modello di distribuzione usato per distribuire la soluzione/applicazione e creare le risorse definite per la soluzione. Per altre informazioni, vedere [come creare i file del modello di distribuzione](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json: questo file viene usato dal portale di Azure per generare l'interfaccia utente per il provisioning di questa soluzione/applicazione. Per altre informazioni, vedere [Creare l'interfaccia utente del portale di Azure per l'applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Questo pacchetto deve contenere gli eventuali modelli annidati o gli script necessari per completare il provisioning di questa applicazione. Il file MainTemplate.json e il file createUIDefinition.json devono trovarsi nella cartella radice.

   ![Dettagli del pacchetto per un modello di soluzione](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>Dettagli del pacchetto per un'applicazione gestita

Specificare i dettagli del pacchetto seguenti:

- **Versione**: la versione del pacchetto che verrà caricato. I tag di versione devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi.
- **File pacchetto (ZIP)**: pacchetto che contiene i file seguenti, salvati in un file ZIP.
  - applianceMainTemplate.json: file del modello di distribuzione usato per distribuire la soluzione/applicazione e creare le risorse definite. Per altre informazioni, vedere [Avvio rapido: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json: questo file viene usato dal portale di Azure per generare l'interfaccia utente per il provisioning di questa soluzione/applicazione. Per altre informazioni, vedere [Creare l'interfaccia utente del portale di Azure per l'applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json: file del modello contenente solo le risorse Microsoft.Solution/appliances. Per altre informazioni, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Prendere nota delle proprietà chiave seguenti di questa risorsa:
    - “kind”: il valore deve essere "Marketplace" in caso di applicazione gestita di Marketplace.
    - “ManagedResourceGroupId”: il gruppo di risorse nella sottoscrizione del cliente in cui vengono distribuite tutte le risorse definite nel file applianceMainTemplate.json.
    - "PublisherPackageId": la stringa che identifica il pacchetto in modo univoco. Questo valore deve essere costruito nel modo seguente: si tratta di una concatenazione di [publisherId].[OfferId]-preview[SKUID].[PackageVersion].

  >[!IMPORTANT] 
  >Questo pacchetto deve contenere gli eventuali modelli annidati o gli script necessari per completare il provisioning di questa applicazione. Questi file devono trovarsi nella cartella radice:  MainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json.

- **ID tenant**: ID tenant di Azure Active Directory dell'organizzazione.
- **Abilita accesso JIT?** : selezionare **Sì** per abilitare l'accesso per la gestione JIT per le distribuzioni dei clienti che usano questa offerta.

  >[!NOTE] 
  >Se si abilita JIT, è necessario aggiornare il file CreateUiDefinition.json per supportare l'accesso JIT.

   ![Dettagli del pacchetto per un'applicazione gestita](./media/azureapp-sku-pkgdetails-managedapplication.png)

Per un'applicazione gestita, è necessario configurare Autorizzazione e Impostazioni dei criteri.

#### <a name="authorization"></a>Authorization

Aggiungere l'identificatore di Azure Active Directory per l'utente, il gruppo o l'applicazione a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite. L'autorizzazione che viene concessa è indicata dall'ID di definizione del ruolo. Può trattarsi di un proprietario, collaboratore o un ruolo personalizzato.

#### <a name="policy-settings"></a>Impostazioni dei criteri

Aggiungere i criteri a cui l'app gestita è conforme. Per altre informazioni sui criteri delle risorse di Azure, vedere [Informazioni su Criteri di Azure](../../../governance/policy/overview.md).


   ![Autorizzazione e impostazioni dei criteri per un'applicazione gestita](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Per creare una nuova autorizzazione:**

1. In **Autorizzazione** selezionare **+ Nuova autorizzazione**.
2. Per **ID entità di sicurezza**, digitare l'identificatore di Azure Active Directory per l'utente, il gruppo o l'applicazione a cui si vuole concedere l'autorizzazione per il gruppo di risorse gestite. L'autorizzazione che viene concessa è indicata da Definizione ruolo.
3. Per **Definizione ruolo**, selezionare una di queste opzioni nell'elenco a discesa:  Proprietario o Collaboratore. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>È possibile aggiungere più autorizzazioni. È tuttavia consigliabile creare un gruppo utenti di Active Directory e specificarne l'ID in "PrincipalId". Consentirà di aggiungere altri utenti al gruppo utenti senza dover aggiornare lo SKU.

**Per creare un nuovo criterio:**

1. In **Impostazioni dei criteri** selezionare **+ Nuovo criterio**.
2. Per **Nome criterio**, immettere un nome per il criterio. La lunghezza massima del nome è di 50 caratteri.
3. Per **Criteri**, selezionare una delle opzioni nell'elenco a discesa. Scegliere i criteri che il provider di dati vuole abilitare quando l'applicazione usa i dati. Per altre informazioni, vedere [Esempi di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Impostazioni dei criteri per un'applicazione gestita](./media/azureapp-sku-policy-settings.png)

4. Per **SKU criteri**, selezionare Gratuito o Standard come tipo di SKU dei criteri. Lo SKU Standard è necessario per i criteri di controllo.

## <a name="next-steps"></a>Passaggi successivi

[Scheda Marketplace](./cpp-marketplace-tab.md)
