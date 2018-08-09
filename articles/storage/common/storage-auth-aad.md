---
title: Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory (anteprima) | Microsoft Docs
description: Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory (anteprima).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 90868961475c2e9d0ac7d28c5d9a50c8eb281675
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525206"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory (anteprima)

Archiviazione di Azure supporta l'autenticazione e l'autorizzazione con Azure Active Directory (AD) per i servizi BLOB e di accodamento. Azure AD consente di usare il controllo degli accessi in base al ruolo per concedere l'accesso a utenti, gruppi o entità servizio dell'applicazione. 

Rispetto ad altre opzioni, l'autorizzazione delle applicazioni che accedono ad Archiviazione di Azure tramite Azure AD offre la massima sicurezza e facilità d'uso. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. In modo analogo, è possibile continuare a usare le firme di accesso condiviso (SAS) per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token SAS o di occuparsi della revoca di una SAS compromessa.

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

Per la versione di anteprima, tenere presente quanto segue:

- L'integrazione di Azure AD è disponibile per i servizi BLOB e di accodamento solo nell'anteprima.
- L'integrazione di Azure AD è disponibile per gli account di archiviazione GPv1, GPv2 e BLOB in tutte le aree pubbliche. 
- Sono supportati solo gli account di archiviazione creati con il modello di distribuzione di Resource Manager. 
- A breve sarà disponibile il supporto per le informazioni sull'identità del chiamante nei log di Analisi archiviazione di Azure.
- L'autorizzazione di Azure AD dell'accesso alle risorse negli account di archiviazione standard è attualmente supportata. L'autorizzazione dell'accesso ai BLOB di pagine negli account di archiviazione Premium sarà supportata a breve.
- Archiviazione di Azure supporta ruoli Controllo degli accessi in base al ruolo predefiniti e personalizzati. È possibile assegnare ruoli nell'ambito della sottoscrizione, del gruppo di risorse, dell'account di archiviazione o di un singolo contenitore o coda.
- Le librerie client di Archiviazione di Azure che attualmente supportano l'integrazione di Azure AD includono:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (usare 7.1.x-Preview)
    - Python
        - [BLOB](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [Coda](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.JS](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> Questa versione di anteprima è destinata solo all'uso in ambienti non di produzione. Non saranno disponibili contratti di servizio per ambienti di produzione finché l'integrazione di Azure AD per Archiviazione di Azure non verrà dichiarata disponibile a livello generale. Se l'integrazione di Azure AD non è ancora supportata per il proprio scenario, continuare a usare l'autorizzazione basata su chiave condivisa o token di firma di accesso condiviso nelle applicazioni.
>
> Durante l'anteprima, la propagazione delle assegnazioni dei ruoli Controllo degli accessi in base al ruolo può richiedere fino a cinque minuti.
>
> L'integrazione di Azure AD con Archiviazione di Azure richiede l'uso di HTTPS per le operazioni di Archiviazione di Azure.

## <a name="get-started-with-azure-ad-for-storage"></a>Introduzione ad Azure AD per Archiviazione

Il primo passaggio per l'uso dell'integrazione di Azure AD con Archiviazione di Azure è costituito dall'assegnazione dei ruoli Controllo degli accessi in base al ruolo per i dati di archiviazione all'entità servizio (un utente, un gruppo o un'entità servizio dell'applicazione) o all'identità del servizio gestita. I ruoli Controllo degli accessi in base al ruolo includono set comuni di autorizzazioni per contenitori e code. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo per Archiviazione di Azure, vedere [Gestire i diritti di accesso ai dati di archiviazione con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md).

Per usare Azure AD per autorizzare l'accesso alle risorse di archiviazione contenute nelle applicazioni è necessario richiedere al codice un token di accesso OAuth 2.0. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste ad Archiviazione di Azure, vedere [Eseguire l'autenticazione con Azure AD da un'applicazione di Archiviazione di Azure (anteprima)](storage-auth-aad-app.md). Se si usa un'identità del servizio gestita di Azure, vedere [Eseguire l'autenticazione con Azure AD da un'identità del servizio gestita di una macchina virtuale di Azure (anteprima)](storage-auth-aad-msi.md).

L'interfaccia della riga di comando di Azure e PowerShell ora supportano l'accesso con un'identità di Azure AD. Dopo l'accesso con un'identità di Azure AD, la sessione viene eseguita con questa identità. Per altre informazioni, vedere [Usare un'identità di Azure AD per accedere ad Archiviazione di Azure con l'interfaccia della riga di comando o PowerShell (anteprima)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione di Azure AD per BLOB e code di Azure, vedere il post di blog del team di Archiviazione di Azure sull'[annuncio della versione di anteprima di Autenticazione di Azure AD per Archiviazione di Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
