---
title: Usare l'identità del servizio gestito in Azure Cloud Shell | Microsoft Docs
description: Autenticare il codice con l'identità del servizio gestito in Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
ms.locfileid: "31516285"
---
# <a name="use-msi-in-azure-cloud-shell"></a>Usare l'identità del servizio gestito in Azure Cloud Shell

Azure Cloud Shell supporta l'autorizzazione con l'identità del servizio gestito. Consente di recuperare i token di accesso per comunicare in modo sicuro con i servizi di Azure.

## <a name="about-managed-service-identity-msi"></a>Informazioni sull'identità del servizio gestito
Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione sicura delle credenziali che devono essere presenti nel codice per l'autenticazione ai servizi cloud. In Cloud Shell potrebbe essere necessario autenticare il recupero da Key Vault per le credenziali necessarie per uno script.

Identità del servizio gestito consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory, ovvero Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

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
