---
title: Usare il controllo degli accessi in base al ruolo per gestire i diritti di accesso a contenitori e code di Archiviazione di Azure (anteprima) | Microsoft Docs
description: Usare il controllo degli accessi in base al ruolo per assegnare ruoli per l'accesso a dati di Archiviazione di Azure a utenti, gruppi, entità servizio dell'applicazione o identità del servizio gestite. Archiviazione di Azure supporta ruoli predefiniti e personalizzati per i diritti di accesso a contenitori e code.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: be3fd304e04a66e6564141b6a20efea88cf62553
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42139950"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Gestire i diritti di accesso a dati di Archiviazione di Azure con il controllo degli accessi in base al ruolo (anteprima)

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview). Archiviazione di Azure definisce un set di ruoli di controllo degli accessi in base al ruolo predefiniti che includono un set comune di autorizzazioni usate per accedere a contenitori o code. Quando un ruolo di controllo degli accessi in base al ruolo viene assegnato a un'identità di Azure AD, all'identità viene concesso l'accesso a queste risorse, in base all'ambito specificato. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. È possibile assegnare diritti di accesso per risorse di Archiviazione di Azure tramite il portale di Azure, strumenti da riga di comando di Azure e API di gestione di Azure. 

Un'identità di Azure AD può essere un utente, un gruppo o un'entità servizio dell'applicazione oppure può essere un'*identità del servizio gestita*. Un'entità di sicurezza può essere un utente, un gruppo o un'entità servizio dell'applicazione. Un'[identità del servizio gestita](../../active-directory/managed-service-identity/overview.md) è un'identità gestita automaticamente usata per l'autenticazione da applicazioni in esecuzione in macchine virtuali, app per le funzioni, set di scalabilità di macchine virtuali di Azure e altro ancora. Per una panoramica dell'identità in Azure AD, vedere [Understand Azure identity solutions](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions) (Informazioni sulle soluzioni di gestione delle identità di Azure).

## <a name="rbac-roles-for-azure-storage"></a>Ruoli di controllo degli accessi in base al ruolo per Archiviazione di Azure

Archiviazione di Azure supporta ruoli di controllo degli accessi in base al ruolo predefiniti e personalizzati. Archiviazione di Azure offre i ruoli predefiniti seguenti da usare con Azure AD:

- [Collaboratore ai dati del BLOB di archiviazione (anteprima)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Lettore dei dati dei BLOB di archiviazione (anteprima)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Collaboratore ai dati della coda di archiviazione (anteprima)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Lettore dei dati della coda di archiviazione (anteprima)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti per Archiviazione di Azure, vedere [Informazioni sulle definizioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

È anche possibile definire ruoli personalizzati da usare con contenitori e code. Per altre informazioni, vedere [Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!IMPORTANT]
> Questa versione di anteprima è destinata solo per l'uso in ambienti non di produzione. Non saranno disponibili contratti di servizio per ambienti di produzione finché l'integrazione di Azure AD per Archiviazione di Azure non verrà dichiarata disponibile a livello generale. Se l'integrazione di Azure AD non è ancora supportata per il proprio scenario, continuare a usare l'autorizzazione con chiave condivisa o token di firma di accesso condiviso nelle applicazioni. Per altre informazioni sulla versione di anteprima, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory (anteprima)](storage-auth-aad.md).
>
> Durante l'anteprima, la propagazione delle assegnazioni dei ruoli di controllo degli accessi in base al ruolo può richiedere fino a cinque minuti.

## <a name="assign-a-role-to-a-security-principal"></a>Assegnare un ruolo a un'entità di sicurezza

È possibile assegnare un ruolo di controllo degli accessi in base al ruolo a un'identità di Azure per concedere le autorizzazioni per contenitori o code nell'account di archiviazione. È possibile definire l'ambito dell'assegnazione di ruolo a livello di account di archiviazione o di una coda o un contenitore specifico. La tabella seguente contiene un riepilogo dei diritti di accesso concessi in base ai ruoli predefiniti, a seconda dell'ambito: 

|                                 |     Collaboratore ai dati del BLOB di archiviazione                                                 |     Lettore dati dei BLOB di archiviazione                                                |     Collaboratore ai dati della coda di archiviazione                                  |     Lettore dei dati della coda di archiviazione                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Ambito a livello di sottoscrizione       |    Accesso in lettura/scrittura a tutti i contenitori e BLOB nella sottoscrizione       |    Accesso in lettura a tutti i contenitori e BLOB nella sottoscrizione       |    Accesso in lettura/scrittura a tutte le code nella sottoscrizione       |    Accesso in lettura a tutte le code nella sottoscrizione         |
|    Ambito a livello di gruppo di risorse     |    Accesso in lettura/scrittura a tutti i contenitori e BLOB nel gruppo di risorse     |    Accesso in lettura a tutti i contenitori e BLOB nel gruppo di risorse     |    Accesso in lettura/scrittura a tutte le code nel gruppo di risorse     |    Accesso in lettura a tutte le code nel gruppo di risorse     |
|    Ambito a livello di account di archiviazione    |    Accesso in lettura/scrittura a tutti i contenitori e BLOB nell'account di archiviazione    |    Accesso in lettura a tutti i contenitori e BLOB nell'account di archiviazione    |    Accesso in lettura/scrittura a tutte le code nell'account di archiviazione    |    Accesso in lettura a tutte le code nell'account di archiviazione    |
|    Ambito a livello di contenitore/coda    |    Accesso in lettura/scrittura al contenitore specificato e ai relativi BLOB              |    Accesso in lettura al contenitore specificato e ai relativi BLOB              |    Accesso in lettura/scrittura alla coda specificata                  |    Accesso in lettura alla coda specificata                    |

> [!NOTE]
> In quanto proprietario dell'account di archiviazione di Azure, non si ricevono automaticamente le autorizzazioni per l'accesso ai dati. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.

Per informazioni dettagliate sulle autorizzazioni necessarie per chiamare operazioni di Archiviazione di Azure, vedere [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Autorizzazioni per chiamare operazioni REST).

Le sezioni seguenti mostrano come assegnare un ruolo con ambito a livello di account di archiviazione o di singolo contenitore.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Assegnare un ruolo con ambito a livello di account di archiviazione nel portale di Azure

Per assegnare un ruolo predefinito che concede l'accesso a tutti i contenitori o le code nell'account di archiviazione tramite il portale di Azure:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
2. Selezionare l'account di archiviazione, quindi selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account. Fare clic sul pulsante **Aggiungi** per aggiungere un nuovo ruolo.

    ![Screenshot che mostra le impostazioni di controllo di accesso per l'archiviazione](media/storage-auth-aad-rbac/portal-access-control.png)

3. Nella finestra **Aggiungi autorizzazioni** selezionare il ruolo da assegnare a un'identità di Azure AD. Eseguire quindi una ricerca per individuare l'identità cui si vuole assegnare il ruolo. Ad esempio, la figura seguente mostra il ruolo **Lettore dei dati dei BLOB di archiviazione (anteprima)** assegnato a un utente.

    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Fare clic su **Save**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, la figura seguente mostra che gli utenti aggiunti hanno ora autorizzazioni di lettura per tutti i dati dei BLOB nell'account di archiviazione.

    ![Screenshot che mostra l'elenco degli utenti assegnati a un ruolo](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Assegnare un ruolo con ambito a livello di singolo contenitore o coda nel portale di Azure

I passaggi per l'assegnazione di un ruolo predefinito con ambito a livello di singolo contenitore o coda sono simili. La procedura descritta qui assegna un ruolo con ambito a livello di singolo contenitore, ma è possibile seguire gli stessi passaggi per assegnare un ruolo con ambito a livello di singola coda: 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione e visualizzare la sezione **Panoramica** per l'account.
2. In Servizio BLOB selezionare **Esplora BLOB**. 
3. Individuare il contenitore per il quale si vuole assegnare un ruolo e visualizzare le impostazioni del contenitore. 
4. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per il contenitore.
5. Nella finestra **Aggiungi autorizzazioni** selezionare il ruolo che si vuole assegnare a un'identità di Azure AD. Eseguire quindi una ricerca per individuare l'identità cui si vuole assegnare il ruolo.
6. Fare clic su **Save**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, la figura seguente mostra che l'utente aggiunto ha ora autorizzazioni di lettura per i dati nel contenitore denominato *sample-container*.

    ![Screenshot che mostra l'elenco degli utenti assegnati a un ruolo](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione al controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md).
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di controllo degli accessi in base al ruolo con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere gli articoli seguenti:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../../role-based-access-control/role-assignments-rest.md)
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di Archiviazione di Azure](storage-auth-aad-app.md).
- Per altre informazioni sull'integrazione di Azure AD per contenitori e code di Azure, vedere il post di blog del team di Archiviazione di Azure sull'[annuncio della versione di anteprima di Autenticazione di Azure AD per Archiviazione di Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 
