---
title: Usare identità gestite per le risorse di Azure in Azure Cloud Shell | Microsoft Docs
description: Autenticare il codice con l'identità del servizio gestito in Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: damaerte
ms.openlocfilehash: 7cadaaf67f9c6923ee9e9eb2596941aa8e1f0c9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199483"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Usare identità gestite per le risorse di Azure in Azure Cloud Shell

Per le risorse di Azure, Azure Cloud Shell supporta l'autorizzazione con identità gestite. Consente di recuperare i token di accesso per comunicare in modo sicuro con i servizi di Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Informazioni sulle identità gestite per le risorse di Azure
Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione sicura delle credenziali che devono essere presenti nel codice per l'autenticazione ai servizi cloud. In Cloud Shell potrebbe essere necessario autenticare il recupero da Key Vault per le credenziali necessarie per uno script.

Le identità gestite per le risorse di Azure consentono di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

## <a name="acquire-access-token-in-cloud-shell"></a>Acquisire un token di accesso in Cloud Shell

Eseguire i comandi seguenti per impostare un token di accesso dell'identità del servizio gestito come variabile di ambiente, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Gestione della scadenza di token

Il sottosistema dell'identità del servizio gestito locale memorizza i token nella cache. È quindi possibile eseguire tutte le chiamate desiderate e una chiamata in transito ad Azure AD restituisce un risultato solo se:
- si verifica un mancato riscontro nella cache a causa dell'assenza di token nella cache
- il token è scaduto

Se si memorizza nella cache il token nel codice, è consigliabile essere preparati a gestire gli scenari in cui la risorsa indica che il token è scaduto.

Per gestire gli errori del token, visitare la [pagina dell'identità del servizio gestito relativa al curl dei token di accesso dell'identità del servizio gestito](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sull'identità del servizio gestito](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Acquisire i token di accesso dall'identità del servizio gestito delle macchine virtuali](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
