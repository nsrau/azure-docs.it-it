---
title: Sottodomini personalizzati
titleSuffix: Azure Cognitive Services
description: I nomi dei sottodomini personalizzati per ogni risorsa del servizio cognitivo vengono creati tramite il portale di Azure, Azure Cloud Shell o l'interfaccia della riga di comando di Azure.Custom subdomain names for each Cognitive Service resource are created through the Azure portal, Azure Cloud Shell, or Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647684"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Nomi di sottodomini personalizzati per Servizi cognitivi

Servizi cognitivi di Azure usano nomi di sottodomini personalizzati per ogni risorsa creata tramite il portale di [Azure,](https://portal.azure.com) [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)o [l'interfaccia della riga](https://docs.microsoft.com/cli/azure/install-azure-cli)di comando di Azure. A differenza degli endpoint regionali, comuni a tutti i clienti in un'area di Azure specifica, i nomi dei sottodomini personalizzati sono univoci per la risorsa. I nomi dei sottodomini personalizzati sono necessari per abilitare funzionalità come Azure Active Directory (Azure AD) per l'autenticazione.

## <a name="how-does-this-impact-existing-resources"></a>In che modo questo influisce sulle risorse esistenti?

Le risorse di Servizi cognitivi create prima del 1 luglio 2019 utilizzeranno gli endpoint regionali per il servizio associato. Questi endpoint funzioneranno con risorse nuove e esistenti.

Se si vuole eseguire la migrazione di una risorsa esistente per sfruttare i nomi dei sottodomini personalizzati, in modo da poter abilitare funzionalità come Azure AD, seguire queste istruzioni:If you're to migrate an existing resource to leverage custom subdomain names, so that you can enable features like Azure AD, follow these instructions:

1. Accedere al portale di Azure e individuare la risorsa Servizi cognitivi a cui si vuole aggiungere un nome di sottodominio personalizzato.
2. Nel pannello **Panoramica** individuare e selezionare **Genera nome di dominio personalizzato**.
3. Si apre un pannello con le istruzioni per creare un sottodominio personalizzato univoco per la risorsa.
   > [!WARNING]
   > Dopo aver creato un nome di sottodominio personalizzato non **può** essere modificato.

## <a name="do-i-need-to-update-my-existing-resources"></a>È necessario aggiornare le risorse esistenti?

No. L'endpoint regionale continuerà a funzionare per servizi cognitivi nuovi ed esistenti e il nome del sottodominio personalizzato è facoltativo. Anche se viene aggiunto un nome di sottodominio personalizzato, l'endpoint regionale continuerà a funzionare con la risorsa.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Cosa succede se un SDK richiede l'area per una risorsa?

> [!WARNING]
> I servizi di riconoscimento vocale **non** supportano al momento sottodomini personalizzati. Utilizzare gli endpoint regionali quando si utilizzano i servizi di riconoscimento vocale e gli SDK associati.

Gli endpoint regionali e i nomi dei sottodomini personalizzati sono entrambi supportati e possono essere utilizzati in modo intercambiabile. Tuttavia, è necessario l'endpoint completo.

Le informazioni sull'area sono disponibili nel pannello **Panoramica** per la risorsa nel portale di Azure.Region information is available in the Overview blade for your resource in the [Azure portal.](https://portal.azure.com) Per l'elenco completo degli endpoint regionali, vedere [Esiste un elenco di endpoint regionali?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>I nomi dei sottodomini personalizzati sono regionali?

Sì. L'utilizzo di un nome di sottodominio personalizzato non modifica gli aspetti regionali della risorsa Servizi cognitivi.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Quali sono i requisiti per un nome di sottodominio personalizzato?

Un nome di sottodominio personalizzato è univoco per la risorsa. Il nome può includere solo `-` caratteri alfanumerici e il carattere; deve avere una lunghezza compresa tra 2 e `-`64 caratteri e non può terminare con un carattere .

## <a name="can-i-change-a-custom-domain-name"></a>Posso modificare un nome di dominio personalizzato?

No. Dopo che un nome di sottodominio personalizzato è stato creato e associato a una risorsa, non è possibile modificarlo.

## <a name="can-i-reuse-a-custom-domain-name"></a>Posso riutilizzare un nome di dominio personalizzato?

Ogni nome di sottodominio personalizzato è univoco, pertanto per riutilizzare un nome di sottodominio personalizzato assegnato a una risorsa di Servizi cognitivi, è necessario eliminare la risorsa esistente. Dopo l'eliminazione della risorsa, è possibile riutilizzare il nome del sottodominio personalizzato.

## <a name="is-there-a-list-of-regional-endpoints"></a>Esiste un elenco di endpoint regionali?

Sì. Si tratta di un elenco di endpoint regionali che è possibile usare con le risorse di Servizi cognitivi di Azure.This is a list of regional endpoints that you can use with Azure Cognitive Services resources.

> [!NOTE]
> L'API Translator Text e le API di Ricerca Bing usano endpoint globali.

| Tipo di endpoint | Region | Endpoint |
|---------------|--------|----------|
| Pubblico | Globale (testo traduttore & Bing) | `https://api.cognitive.microsoft.com` |
| | Australia orientale | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brasile meridionale | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Canada centrale | `https://canadacentral.api.cognitive.microsoft.com` |
| | Stati Uniti centrali | `https://centralus.api.cognitive.microsoft.com` |
| | Asia orientale | `https://eastasia.api.cognitive.microsoft.com` |
| | Stati Uniti orientali | `https://eastus.api.cognitive.microsoft.com` |
| | Stati Uniti orientali 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Francia centrale | `https://francecentral.api.cognitive.microsoft.com` |
| | India centrale | `https://centralindia.api.cognitive.microsoft.com` |
| | Giappone orientale | `https://japaneast.api.cognitive.microsoft.com` |
| | Corea centrale | `https://koreacentral.api.cognitive.microsoft.com` |
| | Stati Uniti centro-settentrionali | `https://northcentralus.api.cognitive.microsoft.com` |
| | Europa settentrionale | `https://northeurope.api.cognitive.microsoft.com` |
| | Sudafrica settentrionale | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Stati Uniti centro-meridionali | `https://southcentralus.api.cognitive.microsoft.com` |
| | Asia sud-orientale | `https://southeastasia.api.cognitive.microsoft.com` |
| | Regno Unito meridionale | `https://uksouth.api.cognitive.microsoft.com` |
| | Stati Uniti centro-occidentali | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa occidentale | `https://westeurope.api.cognitive.microsoft.com` |
| | Stati Uniti occidentali | `https://westus.api.cognitive.microsoft.com` |
| | Stati Uniti occidentali 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov Virginia | `https://virginia.api.cognitive.microsoft.us` |
| Cina | Cina orientale 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Cina settentrionale | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Vedere anche

* [Cosa sono i Servizi cognitivi?](Welcome.md)
* [Autenticazione](authentication.md)
