---
title: "Autenticazione al Registro contenitori di Azure con entità servizio"
description: "Informazioni su come fornire l'accesso alle immagini nel registro del contenitore privato usando un'entità del servizio Azure Active Directory."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 97036ecabceb12b87b76c6ecb7e521157cbef827
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticazione al Registro contenitori di Azure con entità servizio

È possibile usare un'entità servizio Azure Active Directory (Azure AD) per fornire l'accesso `docker push` e `pull` dell'immagine del contenitore al registro contenitori. Se si usa un'entità servizio, è possibile fornire l'accesso ai servizi e alle applicazioni "headless".

## <a name="what-is-a-service-principal"></a>Che cos'è un'entità servizio?

Le *entità servizio* di Azure AD forniscono accesso alle risorse Azure all'interno della sottoscrizione. Un'entità servizio può essere considerata come un'identità utente per un servizio, in cui il "servizio" è qualsiasi applicazione, servizio o piattaforma che deve accedere alle risorse. È possibile configurare un'entità servizio con diritti di accesso limitati solo alle risorse specificate. È possibile configurare l'applicazione o il servizio per usare le credenziali dell'entità servizio per accedere alle risorse.

Nel contesto del Registro contenitori di Azure è possibile creare un'entità servizio Azure AD con le autorizzazioni pull, push e pull o proprietario nel registro Docker privato in Azure.

## <a name="why-use-a-service-principal"></a>Perché usare un'entità servizio?

Usando un'entità servizio Azure AD, è possibile fornire un accesso limitato al registro di sistema del contenitore privato. È possibile creare diverse entità servizio per ogni applicazione o servizio, ciascuna con diritti di accesso personalizzati al registro. Poiché è possibile evitare di condividere le credenziali tra servizi e applicazioni, è possibile ruotare le credenziali o revocare l'accesso solamente all'entità servizio (e quindi all'applicazione) scelta.

Ad esempio, l'applicazione Web può usare un'entità servizio che le fornisce solo l'accesso `pull` all'immagine, mentre il sistema di compilazione può usare un'entità servizio che gli fornisce entrambi gli accessi `push` e `pull`. Se lo sviluppo dell'applicazione cambia proprietario, è possibile ruotare le credenziali dell'entità servizio senza alcun impatto sul sistema di compilazione.

## <a name="when-to-use-a-service-principal"></a>Quando usare un'entità servizio

Usare un'entità servizio per fornire accesso al registro negli **scenari headless**, ovvero per qualsiasi applicazione, servizio o script che deve eseguire le operazioni di push o pull delle immagini del contenitore in modo automatico.

Per l'accesso individuale al registro, ad esempio quando si esegue il pull manuale di un'immagine contenitore alla workstation di sviluppo, usare la propria [identità Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) per l'accesso al registro (ad esempio con [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver garantito a un'entità servizio l'accesso al registro contenitori, è possibile usare le credenziali nelle applicazioni e nei servizi per l'interazione con il registro.

La configurazione delle singole applicazioni per l'utilizzo delle credenziali dell'entità servizio non rientra nell'ambito di questo articolo, tuttavia qui è possibile trovare istruzioni per determinati servizi e piattaforme:

* [Eseguire l'autenticazione con Registro contenitori di Azure dal servizio contenitore di Azure](container-registry-auth-aks.md)
* [Eseguire l'autenticazione con Registro contenitori di Azure dalle istanze di contenitore di Azure](container-registry-auth-aci.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login