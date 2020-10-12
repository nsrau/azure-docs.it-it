---
title: Sportello anteriore di Azure-sicurezza a livello dell'applicazione | Microsoft Docs
description: Questo articolo consente di comprendere il modo in cui la porta anteriore di Azure consente di proteggere e proteggere i backend dell'applicazione
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89399940"
---
# <a name="application-layer-security-with-front-door"></a>Sicurezza del livello dell'applicazione con Frontdoor
Il front-end di Azure offre funzionalità di protezione delle applicazioni Web per salvaguardare le applicazioni Web da attacchi di rete e exploit di vulnerabilità Web comuni, come SQL Injection o cross site scripting (XSS). Abilitata per i front-end HTTP(S), la sicurezza del livello dell'applicazione di Frontdoor viene distribuita a livello globale ed è sempre attiva, permettendo così di bloccare gli attacchi dannosi alla periferia della rete di Azure, molto lontano dai back-end. Grazie a una maggiore sicurezza e all'ottimizzazione delle prestazioni, Frontdoor offre esperienze Web veloci e sicure agli utenti finali.

## <a name="application-protection"></a>Protezione dell'applicazione
La protezione delle applicazioni di Frontdoor viene configurata in tutti gli ambienti perimetrali in tutto il mondo, in linea con le applicazioni, e impedisce automaticamente al traffico non HTTP(S) di raggiungere le applicazioni Web. L'architettura distribuita multi-tenant rende possibile una protezione globale su larga scala senza sacrificare le prestazioni. Per i carichi di lavoro HTTP(S), il servizio di protezione delle applicazioni Web di Frontdoor offre un motore regole avanzato per regole personalizzate, un set di regole preconfigurato contro gli attacchi comuni e la registrazione dettagliata per tutte le richieste corrispondenti a una regola. Sono supportate azioni flessibili, tra cui consentire, bloccare o solo registrazione.

## <a name="custom-access-control-rules"></a>Regole di controllo di accesso personalizzate
- Elenco **di indirizzi IP consentiti e bloccati:** È possibile configurare regole personalizzate per controllare l'accesso alle applicazioni Web in base all'elenco di indirizzi IP del client. Sono supportati sia indirizzi IPv4 che IPv6
- **Controllo di accesso geografico:** è possibile configurare regole personalizzate per controllare l'accesso alle applicazioni Web in base al codice paese di provenienza dell'indirizzo IP
- **Filtro dei parametri HTTP:** è possibile configurare le regole di accesso personalizzate in base ai parametri delle richieste HTTP(S) corrispondenti, inclusi intestazioni, URL e stringhe di query

## <a name="azure-managed-rules"></a>Regole gestite di Azure
- È abilitato per impostazione predefinita un set preconfigurato di regole per la protezione dalle vulnerabilità OWASP più comuni. In fase di anteprima, il set di regole include il controllo di richieste sqli e xss. Verranno aggiunte altre regole. È possibile scegliere di iniziare con l'azione di sola registrazione per verificare che le regole preconfigurate funzionino come previsto per le applicazioni in uso 

## <a name="rate-limiting"></a>Limitazione della frequenza
- Una regola di controllo della frequenza prevede la limitazione di traffico elevato anomalo da qualsiasi indirizzo IP client.  È possibile impostare una soglia o un numero di richieste Web consentite da un indirizzo IP client per la durata di un minuto.

## <a name="centralized-protection-policy"></a>Criteri di protezione centralizzati
- Si possono definire più regole di protezione e aggiungerle ai criteri in ordine di priorità. Le regole personalizzate hanno una priorità maggiore rispetto al set di regole gestito per consentire le eccezioni. All'applicazione Web vengono associati singoli criteri.  Gli stessi criteri di protezione delle applicazioni Web vengono replicati in tutti i server perimetrali in tutte le località, garantendo criteri di sicurezza coerenti in tutte le aree

## <a name="configuration"></a>Configurazione
- Durante l'anteprima, è possibile usare le API REST, PowerShell o l'interfaccia della riga di comando per creare e distribuire i criteri e le regole di protezione delle applicazioni di Frontdoor. L'accesso al portale sarà supportato prima che il servizio diventi disponibile a livello generale. 


## <a name="monitoring"></a>Monitoraggio
Frontdoor offre la possibilità di monitorare le applicazioni Web per prevenire eventuali attacchi usando metriche in tempo reale integrate in Monitoraggio di Azure per tenere traccia degli avvisi e monitorare facilmente le tendenze.

## <a name="pricing"></a>Prezzi
La sicurezza del livello dell'applicazione di Frontdoor è gratuita durante l'anteprima.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
