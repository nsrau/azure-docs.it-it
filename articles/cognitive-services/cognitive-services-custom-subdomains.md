---
title: Sottodomini personalizzati
titleSuffix: Azure Cognitive Services
description: I servizi cognitivi di Azure usano nomi di sottodominio personalizzati per ogni risorsa creata tramite l'interfaccia della riga di comando di portale di Azure, Azure Cloud Shell o Azure. Diversamente dagli endpoint internazionali, che erano comuni per tutti i clienti in una specifica area di Azure, i nomi di sottodominio personalizzati sono univoci per la risorsa. I nomi di sottodominio personalizzati sono necessari per abilitare funzionalità come Azure Active Directory (Azure AD) per l'autenticazione.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 7153735052f96bef65bf3daaccde4eab3e61b0f9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68473051"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Nomi di sottodominio personalizzati per servizi cognitivi

I servizi cognitivi di Azure usano nomi di sottodominio personalizzati per ogni risorsa creata tramite l'interfaccia della riga di comando di [portale di Azure](https://portal.azure.com), [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/)o [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Diversamente dagli endpoint internazionali, che erano comuni per tutti i clienti in una specifica area di Azure, i nomi di sottodominio personalizzati sono univoci per la risorsa. I nomi di sottodominio personalizzati sono necessari per abilitare funzionalità come Azure Active Directory (Azure AD) per l'autenticazione.

## <a name="how-does-this-impact-existing-resources"></a>In che modo questo influisca sulle risorse esistenti?

Le risorse di servizi cognitivi create prima del 1 ° luglio 2019 utilizzeranno gli endpoint regionali per il servizio associato. Questi endpoint funzioneranno con le risorse nuove e esistenti.

Se si vuole eseguire la migrazione di una risorsa esistente per sfruttare i nomi di sottodominio personalizzati, in modo che sia possibile abilitare funzionalità come Azure AD, seguire queste istruzioni:

1. Accedere al portale di Azure e individuare la risorsa Servizi cognitivi a cui si vuole aggiungere un nome di sottodominio personalizzato.
2. Nel pannello **Panoramica** individuare e selezionare **genera nome di dominio personalizzato**.
3. Verrà aperto un pannello con le istruzioni per creare un sottodominio personalizzato univoco per la risorsa.
   > [!WARNING]
   > Dopo aver creato un nome di sottodominio personalizzato, non è **possibile** modificarlo.

## <a name="do-i-need-to-update-my-existing-resources"></a>È necessario aggiornare le risorse esistenti?

No. L'endpoint a livello di area continuerà a funzionare per servizi cognitivi nuovi ed esistenti e il nome del sottodominio personalizzato sarà facoltativo. Anche se viene aggiunto un nome di sottodominio personalizzato, l'endpoint a livello di area continuerà a funzionare con la risorsa.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Che cosa succede se un SDK chiede l'area per una risorsa?

> [!WARNING]
> Al momento i servizi di riconoscimento vocale **non** supportano sottodomini personalizzati. Usare gli endpoint regionali quando si usano i servizi di riconoscimento vocale e gli SDK associati.

Gli endpoint internazionali e i nomi di sottodominio personalizzati sono supportati e possono essere usati in modo interscambiabile. Tuttavia, l'endpoint completo è obbligatorio.

Le informazioni sull'area sono disponibili nel pannello **Panoramica** per la risorsa nel [portale di Azure](https://portal.azure.com). Per l'elenco completo degli endpoint internazionali, vedere la pagina relativa [a un elenco di endpoint internazionali.](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>I nomi di sottodominio personalizzati sono regionali?

Sì. L'uso di un nome di sottodominio personalizzato non comporta la modifica degli aspetti regionali della risorsa Servizi cognitivi.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Quali sono i requisiti per un nome di sottodominio personalizzato?

Un nome di sottodominio personalizzato è univoco per la risorsa. Il nome può includere solo caratteri alfanumerici e `-` il carattere. deve avere una lunghezza compresa tra 2 e 64 caratteri e non può terminare `-`con un.

## <a name="can-i-change-a-custom-domain-name"></a>È possibile modificare un nome di dominio personalizzato?

No. Dopo la creazione e l'associazione di un nome di sottodominio personalizzato a una risorsa, non è possibile modificarlo.

## <a name="can-i-reuse-a-custom-domain-name"></a>È possibile riutilizzare un nome di dominio personalizzato?

Ogni nome di sottodominio personalizzato è univoco, quindi, per riutilizzare un nome di sottodominio personalizzato assegnato a una risorsa di servizi cognitivi, è necessario eliminare la risorsa esistente. Dopo che la risorsa è stata eliminata, è possibile riutilizzare il nome del sottodominio personalizzato.

## <a name="is-there-a-list-of-regional-endpoints"></a>È presente un elenco di endpoint internazionali?

Sì. Questo è un elenco di endpoint internazionali che è possibile usare con le risorse dei servizi cognitivi di Azure.

> [!NOTE]
> Il API Traduzione testuale e API di ricerca Bing utilizzano endpoint globali.

| Tipo di endpoint | Region | Endpoint |
|---------------|--------|----------|
| Public | Globale (Traduzione testuale & Bing) | `https://api.cognitive.microsoft.com` |
| | Australia orientale | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brasile meridionale | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Canada centrale | `https://canadacentral.api.cognitive.microsoft.com` |
| | Stati Uniti centrali | `https://centralus.api.cognitive.microsoft.com` |
| | Asia orientale | `https://eastasia.api.cognitive.microsoft.com` |
| | East US | `https://eastus.api.cognitive.microsoft.com` |
| | Stati Uniti orientali 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Francia centrale | `https://francecentral.api.cognitive.microsoft.com` |
| | India centrale | `https://centralindia.api.cognitive.microsoft.com` |
| | Giappone orientale | `https://japaneast.api.cognitive.microsoft.com` |
| | Corea del Sud centrale | `https://koreacentral.api.cognitive.microsoft.com` |
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

* [Che cosa sono i servizi cognitivi?](Welcome.md)
* [autenticazione](authentication.md)
