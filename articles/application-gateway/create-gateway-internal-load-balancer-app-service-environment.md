---
title: Risolvere i problemi relativi a un gateway applicazione in Azure - Ambiente del servizio app del servizio di bilanciamento del carico interno | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi a un gateway applicazione tramite un servizio di bilanciamento del carico interno con un ambiente del servizio app in Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 16cfe4c1db8fe9ba4c80f6451611237e3ee12c55
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617819"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Il certificato del server back-end non è consentito per un gateway applicazione quando si usa un servizio di bilanciamento del carico interno con un ambiente del servizio app

Questo articolo permette di risolvere questo problema: un certificato non è consentito quando si crea un gateway applicazione tramite un servizio di bilanciamento del carico interno con un ambiente del servizio app nel back-end e si usa SSL end-to-end in Azure.

## <a name="symptoms"></a>Sintomi

Quando si crea un gateway applicazione tramite un servizio di bilanciamento del carico interno con un ambiente del servizio app nel back-end, il server back-end può diventare non integro. Questo problema si verifica se il certificato di autenticazione del gateway applicazione non corrisponde al certificato configurato nel server back-end. Per un esempio, vedere lo scenario seguente:

**Configurazione del gateway applicazione:**

- **Listener:** multisito
- **Porta:** 443
- **Nome host:** test.appgwtestase.com
- **Certificato SSL:** CN=test.appgwtestase.com
- **Pool back-end:** indirizzo IP dell'FQDN
- **Indirizzo IP:** 10.1.5.11
- **Impostazioni HTTP:** HTTPS
- **Porta:**: 443
- **Probe personalizzato:** Nome host - test.appgwtestase.com
- **Certificato di autenticazione:** file CER di test.appgwtestase.com
- **Integrità del back-end:** Danneggiato - Il certificato del server back-end non è consentito con il gateway applicazione.

**Configurazione dell'ambiente del servizio app:**

- **Indirizzo IP del servizio di bilanciamento del carico interno:** 10.1.5.11
- **Nome di dominio:** appgwtestase.com
- **Servizio app:** test.appgwtestase.com
- **Associazione SSL:** SNI SSL - CN=test.appgwtestase.com

Quando si accede al gateway applicazione, viene visualizzato il messaggio di errore seguente perché il server back-end non è integro:

**502 - Il server Web, in funzione come gateway o proxy, ha ricevuto una risposta non valida**.

## <a name="solution"></a>Soluzione

Quando non si usa un nome host per accedere a un sito Web HTTPS, il server back-end restituisce il certificato configurato nel sito Web predefinito. Per un ambiente del servizio app del servizio di bilanciamento del carico interno, il certificato predefinito proviene dal certificato del servizio di bilanciamento del carico interno. Se per il servizio di bilanciamento del carico interno non è configurato alcun certificato, il certificato proviene dal certificato dell'app dell'ambiente del servizio app.

Quando si usa un nome di dominio completo (FQDN) per accedere al servizio di bilanciamento del carico interno, il server back-end restituisce il certificato corretto caricato nelle impostazioni HTTP. In questo caso, tenere presenti le opzioni seguenti:

- Usare l'FQDN nel pool back-end del gateway applicazione in modo che punti all'indirizzo IP del servizio di bilanciamento del carico interno. Questa opzione funziona solo se è stato configurato un DNS personalizzato o una zona DNS privata. In caso contrario, è necessario creare un record "A" per un DNS pubblico.

- Usare il certificato caricato nel servizio di bilanciamento del carico interno o il certificato predefinito nelle impostazioni HTTP. Il gateway applicazione ottiene il certificato quando accede all'indirizzo IP del servizio di bilanciamento del carico interno per il probe.

- Usare un certificato con caratteri jolly nel servizio di bilanciamento del carico interno nel server back-end.

- Deselezionare l'opzione **Use for App service** (Usa per servizio app) per il gateway applicazione.

Per ridurre il sovraccarico, è possibile caricare il certificato del servizio di bilanciamento del carico interno nelle impostazioni HTTP per il funzionamento del percorso del probe. Questo passaggio è solo per l'inserimento del certificato nell'elenco dei certificati consentiti. Non verrà usato per le comunicazioni SSL. È possibile recuperare il certificato del servizio di bilanciamento del carico interno accedendo al servizio di bilanciamento del carico interno con il relativo indirizzo IP su HTTPS, quindi esportando il certificato SSL in un formato CER con codifica Base-64 e caricando il certificato nelle relative impostazioni HTTPS.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
