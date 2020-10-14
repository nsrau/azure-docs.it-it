---
title: Creare un lab in Azure DevTest Labs | Documentazione Microsoft
description: Questo articolo illustra il processo di creazione di un Lab usando il portale di Azure e la Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 962997bcc66188c66fd9db856fe44e4926f8e70c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019648"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creare un lab di sviluppo/test di Azure

Un lab in Azure DevTest Labs è l'infrastruttura che comprende un gruppo di risorse, ad esempio macchine virtuali, e ne consente una migliore gestione tramite la specifica di limiti e quote. Questo articolo illustra la creazione di un lab con il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per creare un lab sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Per altre informazioni sulle opzioni di acquisto di Azure, vedere [Come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). È necessario essere il proprietario della sottoscrizione per creare il lab.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Iniziare a usare Azure DevTest Labs in pochi minuti

Facendo clic sul collegamento seguente, si verrà trasferiti alla pagina portale di Azure che consente di iniziare a creare un nuovo Lab in Azure DevTest Labs.

[Iniziare a usare Azure DevTest Labs in pochi minuti](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Compila le impostazioni per il nuovo account

Nella pagina **Crea un DevTest Labs** compilare le impostazioni seguenti.

> [!TIP]
> Nella parte inferiore di ogni pagina è presente un collegamento che consente di **scaricare un modello per l'automazione**.

### <a name="basic-settings"></a>Impostazioni di base

Per impostazione predefinita, viene visualizzata la scheda **impostazioni di base** . Compilare i valori seguenti:

|Nome|Descrizione|
|---|---|
|**Sottoscrizione** | Obbligatorio. Selezionare la **Sottoscrizione** da associare al lab.|
|**Gruppo di risorse**| Obbligatorio. Immettere un **nome per il gruppo di risorse** del lab. Crearne uno nuovo, se non ne esiste uno.|
|**Nome Lab**| Obbligatorio. Immettere un **nome** per il Lab.|
|**Posizione**|Obbligatorio. Selezionare una posizione in cui archiviare il Lab.|
|**Ambienti pubblici**| Vedere [configurare e usare gli ambienti pubblici](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Impostazioni di arresto automatico

Passare alla pagina **arresto automatico** per visualizzare le relative impostazioni. L'arresto automatico consente di arrestare automaticamente tutti i computer di un Lab a un'ora pianificata ogni giorno.

Nella pagina è possibile abilitare l' **arresto automatico** e definire i parametri per l'arresto automatico di tutte le macchine virtuali del Lab. La funzionalità di arresto automatico è essenzialmente una funzionalità di riduzione dei costi che consente di specificare quando arrestare automaticamente la macchina virtuale. È possibile modificare le impostazioni di arresto automatico dopo la creazione del lab seguendo la procedura descritta nell'articolo [Gestire tutti i criteri per un lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Rete

Quando si crea un Lab, viene creata automaticamente una rete predefinita.

Passare alla scheda **rete** per modificare/configurare l'impostazione desiderata. Ad esempio, selezionare una rete virtuale esistente.

### <a name="tags"></a>Tag

Immettere le informazioni **NOME** e **VALORE** per **Tag** se si vuole creare un contrassegno personalizzato da aggiungere a ogni risorsa che si creerà nel lab. I tag sono utili per gestire e organizzare le risorse del lab per categoria. Per altre informazioni sui tag, ad esempio come aggiungere tag dopo avere creato il lab, vedere [Aggiungere tag a un lab](devtest-lab-add-tag.md).

### <a name="review-and-create"></a>Rivedi e crea

Al termine, selezionare **Crea**. È possibile monitorare lo stato del processo di creazione Lab osservando l'area **notifiche** nella parte superiore destra della pagina del portale. 

## <a name="completed-the-creation"></a>Creazione completata

Al termine, il pulsante **Vai alla risorsa** viene visualizzato nella parte inferiore della pagina e nella finestra di notifica. Al termine aggiornare la pagina **DevTest Labs** per visualizzare il lab appena creato nell'elenco dei lab.  

Premere il pulsante **Vai a risorsa per passare** alla Home page del nuovo account DevTest Labs.

È anche possibile cercare **DevTest Labs** nella portale di Azure. Selezionare il nuovo account dall'elenco e ottenere la home page. 

## <a name="next-steps"></a>Passaggi successivi

Una volta creato il lab, ecco alcuni passaggi successivi da considerare:

* [Proteggere l'accesso a un Lab](devtest-lab-add-devtest-user.md)
* [Imposta criteri Lab](devtest-lab-set-lab-policy.md)
* [Creare un modello Lab](devtest-lab-create-template.md)
* [Creare elementi personalizzati per le macchine virtuali](devtest-lab-artifact-author.md)
* [Aggiungere una VM a un lab](devtest-lab-add-vm.md)

