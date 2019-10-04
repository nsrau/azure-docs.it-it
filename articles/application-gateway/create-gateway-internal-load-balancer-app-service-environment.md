---
title: Risolvere i problemi relativi a un gateway applicazione in Azure - Ambiente del servizio app del servizio di bilanciamento del carico interno | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi a un gateway applicazione tramite un servizio di bilanciamento del carico interno con un ambiente del servizio app in Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 9c3216af283ebd9d84a5469d4d50d18c19f67534
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121945"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Il certificato del server back-end non è consentito per un gateway applicazione quando si usa un servizio di bilanciamento del carico interno con un ambiente del servizio app

Questo articolo spiega come risolvere il problema seguente: Un certificato non è consentito quando si crea un gateway applicazione tramite un servizio di bilanciamento del carico interno con un ambiente del servizio app di Azure nel back-end usando SSL end-to-end in Azure.

## <a name="symptoms"></a>Sintomi

Quando si crea un gateway applicazione tramite un servizio di bilanciamento del carico interno con un ambiente del servizio app nel back-end, il server back-end può diventare non integro. Questo problema si verifica se il certificato di autenticazione del gateway applicazione non corrisponde al certificato configurato nel server back-end. Per un esempio, vedere lo scenario seguente:

**Configurazione del gateway applicazione:**

- **Listener:** multisito
- **Porta:** 443
- **Nome host:** test.appgwtestase.com
- **Certificato SSL**: CN=test.appgwtestase.com
- **Pool back-end**: indirizzo IP o FQDN
- **Indirizzo IP**: 10.1.5.11
- **Impostazioni HTTP**: HTTPS
- **Porta**: 443
- **Probe personalizzato**: nome host, test.appgwtestase.com
- **Certificato di autenticazione:** file CER di test.appgwtestase.com
- **Integrità del back-end**: non integro. Il certificato del server back-end non è consentito con il gateway applicazione.

**Configurazione dell'ambiente del servizio app:**

- **Indirizzo IP del servizio di bilanciamento del carico interno**: 10.1.5.11
- **Nome di dominio:** appgwtestase.com
- **Servizio app:** test.appgwtestase.com
- **Associazione SSL**: SNI SSL - CN=test.appgwtestase.com

Quando si accede al gateway applicazione, viene visualizzato il messaggio di errore seguente perché il server back-end non è integro:

**502 - Il server Web, in funzione come gateway o proxy, ha ricevuto una risposta non valida**.

## <a name="solution"></a>Soluzione

Quando non si usa un nome host per accedere a un sito Web HTTPS, il server back-end restituisce il certificato configurato nel sito Web predefinito, se SNI è disabilitato. Per un ambiente del servizio app del servizio di bilanciamento del carico interno, il certificato predefinito proviene dal certificato del servizio di bilanciamento del carico interno. Se per il servizio di bilanciamento del carico interno non è configurato alcun certificato, il certificato proviene dal certificato dell'app dell'ambiente del servizio app.

Quando si usa un nome di dominio completo (FQDN) per accedere al servizio di bilanciamento del carico interno, il server back-end restituisce il certificato corretto caricato nelle impostazioni HTTP. In caso contrario, tenere presenti le opzioni seguenti:

- Usare l'FQDN nel pool back-end del gateway applicazione in modo che punti all'indirizzo IP del servizio di bilanciamento del carico interno. Questa opzione funziona solo se è stato configurato un DNS personalizzato o una zona DNS privata. In caso contrario, è necessario creare un record "A" per un DNS pubblico.

- Usare il certificato caricato nel servizio di bilanciamento del carico interno o il certificato predefinito (certificato ILB) nelle impostazioni HTTP. Il gateway applicazione ottiene il certificato quando accede all'indirizzo IP del servizio di bilanciamento del carico interno per il probe.

- Usare un certificato con caratteri jolly nel servizio di bilanciamento del carico interno e nel server back-end, in modo che il certificato sia comune per tutti i siti Web. Tuttavia, questa soluzione è possibile solo in caso di sottodomini e non se ognuno dei siti Web richiede nomi host diversi.

- Deselezionare l'opzione **Use for App service** (Usa per servizio app) per il gateway applicazione se si usa l'indirizzo IP del servizio di bilanciamento del carico interno.

Per ridurre il sovraccarico, è possibile caricare il certificato del servizio di bilanciamento del carico interno nelle impostazioni HTTP per il funzionamento del percorso del probe. Questo passaggio è solo per l'inserimento del certificato nell'elenco dei certificati consentiti. Non verrà usato per le comunicazioni SSL. È possibile recuperare il certificato del servizio di bilanciamento del carico interno accedendo al servizio di bilanciamento del carico interno con il suo indirizzo IP dal browser su HTTPS, quindi esportando il certificato SSL in un formato CER con codifica Base-64 e caricandolo nelle impostazioni HTTPS corrispondenti.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contatta il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
