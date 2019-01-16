---
title: Assegnare un accesso ai dati di Gestione costi di Azure | Microsoft Docs
description: Questo articolo illustra in modo dettagliato l'assegnazione di autorizzazioni per i dati di Gestione costi di Azure per vari ambiti di accesso.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: 50f048dc36b0248cdbbd85d91c00b7947f9ddc1f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052139"
---
# <a name="assign-access-to-cost-management-data"></a>Assegnare l’accesso ai dati di Gestione costi

Per la maggior parte degli utenti, una combinazione di autorizzazioni concesse nel portale di Azure e nel portale Enterprise (EA) definisce il livello di accesso dell’utente ai dati di Gestione costi di Azure. Questo articolo illustra l'assegnazione dell'accesso ai dati di Gestione costi. Dopo l'assegnazione della combinazione di autorizzazioni, l’utente può visualizzare i dati in Gestione costi in base al tipo di accesso di cui dispone e all’ambito che ha selezionato nel portale di Azure.

L'ambito selezionato dall’utente viene usato in Gestione costi per fornire il consolidamento dei dati e per controllare l'accesso alle informazioni sui costi. Quando si usano gli ambiti, gli utenti non possono selezionarne più di uno. Possono tuttavia selezionare un ambito più ampio al quale si riferiscono ambiti secondari e quindi filtrare i contenuti che desiderano visualizzare. È importante comprendere il consolidamento dei dati perché alcuni utenti non devono avere accesso a un ambito padre a cui appartengono ambiti figlio.

## <a name="cost-management-scopes"></a>Ambiti di Gestione costi

È necessario avere l'accesso almeno in lettura a uno o più degli ambiti seguenti per visualizzare i dati dei costi.

| **Ambito** | **Definito in** | **Accesso richiesto per visualizzare i dati** | **Impostazione prerequisita del Contratto Enterprise** | **Consolida dati in** |
| --- | --- | --- | --- | --- |
| Account di fatturazione<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Amministratore aziendale | Nessuna | Tutte le sottoscrizioni del Contratto Enterprise |
| department | [https://ea.azure.com](https://ea.azure.com/) | Amministratore del reparto | **Visualizzazione addebiti addebitata per gli amministratori di reparto** | Tutte le sottoscrizioni che appartengono a un account di registrazione collegato al reparto |
| Account di registrazione<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Proprietario dell'account | **Visualizzazione addebiti abilitata per i proprietari dell'account** | Tutte le sottoscrizioni dell'account di registrazione |
| Gruppo di gestione | [https://portal.azure.com](https://portal.azure.com/) | Lettore Gestione costi (o Lettore) | **Visualizzazione addebiti abilitata per i proprietari dell'account** | Tutte le sottoscrizioni incluse nel gruppo di gestione |
| Sottoscrizione | [https://portal.azure.com](https://portal.azure.com/) | Lettore Gestione costi (o Lettore) | **Visualizzazione addebiti abilitata per i proprietari dell'account** | Tutte le risorse o i gruppi di risorse inclusi nella sottoscrizione |
| Gruppo di risorse | [https://portal.azure.com](https://portal.azure.com/) | Lettore Gestione costi (o Lettore) | **Visualizzazione addebiti abilitata per i proprietari dell'account** | Tutte le risorse nel gruppo di risorse |

<sup>1</sup> L'account di fatturazione viene anche indicato con il termine Contratto Enterprise o Registrazione.

<sup>2</sup> L'account di registrazione viene anche indicato come proprietario dell'account.

Il diagramma seguente illustra la relazione tra gli ambiti di Gestione costi con i ruoli e le impostazioni del portale EA.

![Diagramma che illustra la relazione tra gli ambiti di Gestione costi con i ruoli e le impostazioni del portale EA](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Abilitare l'accesso ai costi nel portale EA

Per l'ambito del reparto di fatturazione, l'opzione **Visualizzazione addebiti abilitata per gli amministratori di reparto** deve essere **abilitata** nel portale EA. Per tutti gli altri ambiti, l’opzione **Visualizzazione addebiti abilitata per i proprietari dell'account** deve essere **abilitata** nel portale EA.

Per abilitare un'opzione:

1. Accedere al portale EA dalla pagina [https://ea.azure.com](https://ea.azure.com) con un account di amministratore aziendale.
2. Nel riquadro sinistro, selezionare **Gestisci**.
3. Per gli ambiti di Gestione costi ai quali si desidera fornire l’accesso, abilitare l’opzione **Visualizzazione addebiti abilitata per gli amministratori di reparto** e/o **Visualizzazione addebiti abilitata per i proprietari dell'account**.  
    ![Scheda di registrazione che mostra le opzioni di visualizzazione addebiti per amministratori di reparto e per i proprietari dell’account](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Una volta abilitate le opzioni di visualizzazione addebiti, per la maggior parte degli ambiti è anche necessario configurare l'autorizzazione di controllo degli accessi in base al ruolo (RBAC) nel portale di Azure.

## <a name="enterprise-administrator-role"></a>Ruolo Amministratore azienda

Per impostazione predefinita, un amministratore aziendale ha accesso all'account di fatturazione (contratto/registrazione Enterprise) e a tutti gli altri ambiti, ovvero gli ambiti figlio. L'amministratore aziendale assegna l'accesso agli ambiti agli altri utenti. Come procedura consigliata per la continuità aziendale, è opportuno avere sempre due utenti con accesso come amministratore aziendale. Le sezioni seguenti sono esempi di procedura dettagliata per assegnare gli ambiti agli altri utenti da parte dell’amministratore aziendale.

## <a name="assign-billing-account-scope-access"></a>Assegnare l'accesso all'ambito account di fatturazione

Per accedere all'ambito dell'account di fatturazione è necessaria l'autorizzazione dell’amministratore aziendale nel portale EA. L'amministratore aziendale può accedere e visualizzare i costi per l'intera registrazione EA o più registrazioni. Nel portale di Azure per l'ambito dell'account di fatturazione non è richiesta alcuna azione.

1. Accedere al portale EA dalla pagina [https://ea.azure.com](https://ea.azure.com) con un account di amministratore aziendale.
2. Nel riquadro sinistro, selezionare **Gestisci**.
3. Nella scheda **Registrazione**, selezionare la registrazione che si desidera gestire.  
    ![Selezionare la registrazione nel portale EA](./media/assign-access-acm-data/ea-portal.png)
4. Fare clic su **+ Aggiungi amministratore**.
5. Nella casella Aggiungi amministratore, selezionare il tipo di autenticazione e digitare l'indirizzo di posta elettronica dell'utente.
6. Se l'utente deve avere accesso in sola lettura ai dati di utilizzo e costi, sotto **Sola lettura**, selezionare **Sì**.  In caso contrario, selezionare **No**.
7. Fare clic su **Aggiungi** per creare l'account.  
    ![Informazioni di esempio visualizzate nella casella Aggiungi amministratore](./media/assign-access-acm-data/add-admin.png)

Potrebbero occorrere fino a 30 minuti prima che il nuovo utente possa accedere ai dati di Gestione costi.

### <a name="assign-department-scope-access"></a>Assegnare l'accesso all’ambito di reparto

Per l’accesso all'ambito di reparto è richiesto l'accesso come amministratore di reparto (Visualizzazione addebiti abilitata per gli amministratori di reparto) nel portale EA. L'amministratore di reparto può accedere per visualizzare i costi e i dati di utilizzo associati a un reparto o a più reparti. I dati del reparto comprendono tutte le sottoscrizioni che appartengono a un account di registrazione collegate al reparto. Nel portale di Azure non è richiesta alcuna azione.

1. Accedere al portale EA dalla pagina [https://ea.azure.com](https://ea.azure.com) con un account di amministratore aziendale.
2. Nel riquadro sinistro, selezionare **Gestisci**.
3. Nella scheda **Registrazione**, selezionare la registrazione che si desidera gestire.
4. Fare clic sulla scheda **Reparto**, quindi su **Aggiungi amministratore**.
5. Nella casella Aggiungi amministratore di reparto, selezionare il tipo di autenticazione e digitare l'indirizzo di posta elettronica dell'utente.
6. Se l'utente deve avere accesso in sola lettura ai dati di utilizzo e costi, sotto **Sola lettura**, selezionare **Sì**.  In caso contrario, selezionare **No**.
7. Selezionare i reparti per i quali si desidera concedere l’autorizzazione amministrativa.
8. Fare clic su **Aggiungi** per creare l'account.  
    ![Immettere le informazioni necessarie nella casella Aggiungi Amministratore del reparto](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Assegnare l'accesso all'ambito di account di registrazione

Per l’accesso all'ambito di account di registrazione è richiesto l'accesso come proprietario dell'account (Visualizzazione addebiti abilitata per i proprietari dell'account) nel portale EA. Il proprietario dell'account può visualizzare i costi e i dati di utilizzo associati alle sottoscrizioni create dall'account di registrazione. Nel portale di Azure non è richiesta alcuna azione.

1. Accedere al portale EA dalla pagina [https://ea.azure.com](https://ea.azure.com) con un account di amministratore aziendale.
2. Nel riquadro sinistro, selezionare **Gestisci**.
3. Nella scheda **Registrazione**, selezionare la registrazione che si desidera gestire.
4. Fare clic sulla scheda **Account**, quindi su **Aggiungi account**.
5. Nella casella Aggiungi account, è possibile selezionare il **reparto** al quale associare l’account.
6. Selezionare il tipo di autenticazione e digitare il nome dell'account.
7. Digitare l'indirizzo di posta elettronica dell'utente e, facoltativamente, digitare il centro di costo.
8. Fare clic su **Aggiungi** per creare l'account.  
    ![Immettere le informazioni necessarie nella casella Aggiungi account per un account di registrazione](./media/assign-access-acm-data/add-account.png)

Dopo aver completato i passaggi precedenti, l'account utente diventa un account di registrazione nel portale aziendale e può creare sottoscrizioni. L'utente può accedere ai dati relativi all'utilizzo e ai costi per le sottoscrizioni create.

## <a name="assign-management-group-scope-access"></a>Assegnare l'accesso all’ambito Gruppo di gestione

Per l'accesso all'ambito Gruppo di gestione è richiesta almeno l'autorizzazione Lettore Gestione costi (o Lettore). È possibile configurare le autorizzazioni per un gruppo di gestione nel portale di Azure. Per poter abilitare l'accesso per altri utenti, è necessario disporre almeno dell'autorizzazione Amministratore accessi utente (o Proprietario) per il gruppo di gestione. Inoltre, è necessario abilitare l’impostazione **Visualizzazione addebiti abilitata per i proprietari dell'account** nel portale EA.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nella barra laterale, cercare _Gruppi di gestione_, quindi selezionare **Gruppi di gestione**.
3. Selezionare i gruppi di gestione nella gerarchia.
4. Accanto al nome del gruppo di gestione, fare clic su **Dettagli**.
5. Selezionare **Controllo di accesso (IAM)** dal riquadro a sinistra.
6. Fare clic su **Aggiungi**.
7. Sotto **Ruolo**, selezionare **Lettore Gestione costi**.
8. Per **Assegna l’accesso a**, selezionare **Applicazione, gruppo o utente di Azure AD**.
9. Per assegnare l'accesso, cercare e quindi selezionare l'utente.
10. Fare clic su **Save**.  
    ![Informazioni di esempio nella casella Aggiungi autorizzazioni per un gruppo di gestione](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Assegnare l'accesso all'ambito di sottoscrizione

Per accedere a una sottoscrizione è richiesta almeno l'autorizzazione Lettore Gestione costi (o Lettore). È possibile assegnare l'autorizzazione a una sottoscrizione nel portale di Azure. Per poter abilitare l'accesso per altri utenti, è necessario disporre almeno dell'autorizzazione Amministratore accessi utente (o Proprietario) per la sottoscrizione. Inoltre, è necessario abilitare l’impostazione **Visualizzazione addebiti abilitata per i proprietari dell'account** nel portale EA.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nella barra laterale, cercare _Sottoscrizioni_, quindi selezionare **Sottoscrizioni**.
3. Selezionare la propria sottoscrizione.
4. Selezionare **Controllo di accesso (IAM)** dal riquadro a sinistra.
5. Fare clic su **Aggiungi**.
6. Sotto **Ruolo**, selezionare **Lettore Gestione costi**.
7. Per **Assegna l’accesso a**, selezionare **Applicazione, gruppo o utente di Azure AD**.
8. Per assegnare l'accesso, cercare e quindi selezionare l'utente.
9. Fare clic su **Save**.

## <a name="assign-resource-group-scope-access"></a>Assegnare l'accesso all’ambito Gruppo di risorse

Per accedere a un gruppo di risorse è richiesta almeno l'autorizzazione Lettore Gestione costi (o Lettore). È possibile assegnare le autorizzazioni a un gruppo di risorse nel portale di Azure. Per poter abilitare l'accesso per altri utenti, è necessario disporre almeno dell'autorizzazione Amministratore accessi utente (o Proprietario) per il gruppo di risorse. Inoltre, è necessario abilitare l’impostazione **Visualizzazione addebiti abilitata per i proprietari dell'account** nel portale EA.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nella barra laterale, cercare _Gruppi di risorse_, quindi selezionare **Gruppi di risorse**.
3. Selezionare un gruppo di risorse.
4. Selezionare **Controllo di accesso (IAM)** dal riquadro a sinistra.
5. Fare clic su **Aggiungi**.
6. Sotto **Ruolo**, selezionare **Lettore Gestione costi**.
7. Per **Assegna l’accesso a**, selezionare **Applicazione, gruppo o utente di Azure AD**.
8. Per assegnare l'accesso, cercare e quindi selezionare l'utente.
9. Fare clic su **Save**.

## <a name="next-steps"></a>Passaggi successivi

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
