---
title: Creare un'entità servizio per Azure Stack | Microsoft Docs
description: Viene descritto come creare un'entità servizio che può essere utilizzata con il controllo di accesso basato sui ruoli in Azure Resource Manager per gestire l'accesso alle risorse.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 3a6fa631fdf3436dc3a76817d2c0043b0407b2ce
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121554"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Concedere alle applicazioni di accedere alle risorse di Azure Stack tramite la creazione di entità servizio

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile assegnare un'applicazione l'accesso alle risorse di Azure Stack tramite la creazione di un servizio principale che usa Azure Resource Manager. Un'entità servizio consente di delegare le autorizzazioni specifiche usando [controllo degli accessi in base al ruolo](azure-stack-manage-permissions.md).

Come procedura consigliata, è consigliabile usare le entità servizio per le applicazioni. Le entità servizio sono preferibili per l'esecuzione di un'app usando le proprie credenziali per i motivi seguenti:

* È possibile assegnare autorizzazioni all'entità che sono diverse dalle proprie autorizzazioni dell'account servizio. In genere, le autorizzazioni di un'entità servizio sono limitate a esattamente ciò che l'app deve eseguire.
* Non devi modificare le credenziali dell'app, se il ruolo o le responsabilità cambiano.
* È possibile usare un certificato per automatizzare l'autenticazione quando si esegue uno script automatico.

## <a name="example-scenario"></a>Scenario di esempio

Hai un'app di Gestione configurazione che deve eseguire l'inventario delle risorse di Azure usando Azure Resource Manager. È possibile creare un'entità servizio e assegnarlo al ruolo lettore. Questo ruolo offre l'accesso di sola lettura di app per le risorse di Azure.

## <a name="getting-started"></a>Introduzione

Usare i passaggi descritti in questo articolo come guida per:

* Creare un'entità servizio per l'app.
* Registrare l'app e creare una chiave di autenticazione.
* Assegnare l'app a un ruolo.

La modalità usata per configurare Active Directory per Azure Stack determina come creare un'entità servizio. Selezionare una delle opzioni seguenti:

* Creare un'entità per un servizio [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Creare un'entità per un servizio [Active Directory Federation Services (ADFS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

I passaggi per l'assegnazione di un'entità servizio a un ruolo corrispondente per Azure AD e AD FS. Dopo aver creato l'entità servizio, è possibile [delegare le autorizzazioni](azure-stack-create-service-principals.md) assegnandolo a un ruolo.

## <a name="create-service-principal-for-azure-ad"></a>Creare un'entità servizio per Azure AD

Se lo Stack di Azure Usa Azure AD come archivio di identità, è possibile creare un'entità servizio usando la stessa procedura come in Azure, usando il portale di Azure.

> [!NOTE]
> Verificare di avere il [autorizzazioni di Azure AD necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) prima di iniziare la creazione di un'entità servizio.

### <a name="create-service-principal"></a>Creare un'entità servizio

Per creare un'entità servizio per l'applicazione:

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **registrazioni per l'App** > **Aggiungi**.
3. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** o **Nativa** come tipo di applicazione da creare. Dopo aver impostato i valori selezionare **Crea**.

### <a name="get-credentials"></a>Ottenere le credenziali

Durante l'accesso a livello di codice, usare l'ID per l'applicazione e una chiave di autenticazione. Per ottenere questi valori:

1. Da **Registrazioni dell'app** in Active Directory selezionare l'applicazione.

2. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Le applicazioni nelle applicazioni di esempio usano **id client** quando si fa riferimento per il **ID applicazione**.

     ![ID dell'applicazione per l'applicazione](./media/azure-stack-create-service-principals/image12.png)
3. Per generare una chiave di autenticazione selezionare **Chiavi**.

4. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

> [!IMPORTANT]
> Dopo aver salvato la chiave, il tasto **valore** viene visualizzato. Annotare questo valore perché non è possibile recuperare la chiave in un secondo momento. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

![Avviso di valore della chiave per la chiave salvata.](./media/azure-stack-create-service-principals/image15.png)

Il passaggio finale consiste [assegnazione di un ruolo applicazione](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-ad-fs"></a>Creare un'entità servizio per AD FS

Se è stato distribuito tramite AD FS come archivio identità di Azure Stack, è possibile usare PowerShell per le attività seguenti:

* Creare un'entità servizio.
* Assegnare l'entità servizio a un ruolo.
* Accedere utilizzando l'identità dell'entità servizio.

Per informazioni dettagliate su come creare l'entità servizio, vedere [creare un'entità servizio per AD FS](../azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Assegnare l'entità servizio a un ruolo

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [RBAC: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

> [!NOTE]
> È possibile impostare l'ambito del ruolo a livello di una sottoscrizione, un gruppo di risorse o una risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Un'app con il ruolo lettore per un gruppo di risorse, ad esempio, significa che l'app può leggere tutte le risorse nel gruppo di risorse.

Usare la procedura seguente come guida per l'assegnazione di un ruolo a un'entità servizio.

1. Nel portale di Azure Stack, passare al livello di ambito che si desidera assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**.

2. Selezionare la sottoscrizione a cui assegnare l'applicazione. In questo esempio, la sottoscrizione è Visual Studio Enterprise.

     ![Selezionare la sottoscrizione di Visual Studio Enterprise per l'assegnazione](./media/azure-stack-create-service-principals/image16.png)

3. Selezionare **controllo di accesso (IAM)** per la sottoscrizione.

4. Selezionare **Aggiungi assegnazione ruolo**.

5. Selezionare il ruolo che si desidera assegnare all'applicazione.

6. Cercare l'applicazione e selezionarla.

7. Selezionare **OK** per completare l'assegnazione del ruolo. È possibile visualizzare l'applicazione nell'elenco di utenti assegnati a un ruolo per quell'ambito.

Ora che è stato creato un'entità servizio e assegnato un ruolo, l'applicazione può accedere alle risorse di Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
