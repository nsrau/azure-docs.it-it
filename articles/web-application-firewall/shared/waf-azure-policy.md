---
title: Firewall applicazione Web di Azure e criteri di Azure
description: Il Web Application Firewall (WAF) di Azure combinato con i criteri di Azure può aiutare a applicare gli standard aziendali e a valutare la conformità su larga scala per le risorse di WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: fd474a32b4a517230a82615065d7815c04140045
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432969"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall applicazione Web di Azure e criteri di Azure

Il Web Application Firewall (WAF) di Azure combinato con i criteri di Azure può aiutare a applicare gli standard aziendali e a valutare la conformità su scala per le risorse di WAF. Criteri di Azure è uno strumento di governance che fornisce una visualizzazione aggregata per valutare lo stato complessivo dell'ambiente, con la possibilità di eseguire il drill-down per la granularità per risorsa e per criterio. Criteri di Azure consente anche di portare le risorse alla conformità tramite la correzione bulk per le risorse esistenti e la correzione automatica per le nuove risorse.

## <a name="azure-policy-for-web-application-firewall"></a>Criteri di Azure per il Web Application Firewall

Sono disponibili diverse definizioni di criteri di Azure predefinite per gestire le risorse di WAF. Di seguito è riportata una suddivisione delle definizioni dei criteri e delle relative funzionalità:

1. Il **Web Application Firewall (WAF) deve essere abilitato per il servizio front-end di Azure**: i servizi front-end di Azure vengono valutati in presenza di un WAF presente durante la creazione delle risorse. Il criterio ha tre effetti: audit, Deny e Disable. Audit Tracks quando un servizio di Azure front door non dispone di un WAF e consente agli utenti di visualizzare il servizio di Azure front door non conforme. Deny impedisce la creazione di qualsiasi servizio front door di Azure se non è collegato un WAF. Disabled Disattiva questo criterio.

2. Il **Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione**: i gateway applicazione vengono valutati in presenza di un WAF presente durante la creazione delle risorse. Il criterio ha tre effetti: audit, Deny e Disable. Controlla le tracce quando un gateway applicazione non dispone di un WAF e consente agli utenti di visualizzare il gateway applicazione non conforme. Deny impedisce la creazione di un gateway applicazione se WAF non è collegato. Disabled Disattiva questo criterio.

3. **Il Web Application Firewall (WAF) deve usare la modalità specificata per il servizio front**-end di Azure: impone l'uso della modalità' rilevamento ' o ' prevenzione ' per l'attivazione in tutti i criteri del Web Application Firewall per il servizio front door di Azure. Il criterio ha tre effetti: audit, Deny e Disable. Controlla le tracce quando un WAF non rientra nella modalità specificata. Deny impedisce la creazione di WAF se non è in modalità corretta. Disabled Disattiva questo criterio.

4. **Il Web Application Firewall (WAF) deve usare la modalità specificata per il gateway applicazione**: impone l'uso della modalità' rilevamento ' o ' prevenzione ' in tutti i criteri del firewall applicazione Web per il gateway applicazione. Il criterio ha tre effetti: audit, Deny e Disable. Controlla le tracce quando un WAF non rientra nella modalità specificata. Deny impedisce la creazione di WAF se non è in modalità corretta. Disabled Disattiva questo criterio.

## <a name="launch-an-azure-policy"></a>Avviare un criterio di Azure

1.  Nel home page di Azure digitare Policy nella barra di ricerca e fare clic sull'icona criteri di Azure

2.  Nel servizio criteri di Azure, in **creazione e modifica**, selezionare **assegnazioni**.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Scheda assegnazioni in criteri di Azure":::

3.  Nella pagina assegnazioni selezionare l'icona **assegna criterio** nella parte superiore.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Scheda nozioni di base nella pagina Assegna criteri":::

4.  Nella scheda assegna nozioni di base della pagina dei criteri aggiornare i campi seguenti:
    1.  **Ambito**: selezionare le sottoscrizioni di Azure e i gruppi di risorse interessati dalla definizione dei criteri.
    2.  **Esclusioni**: selezionare le risorse dall'ambito da escludere dall'assegnazione dei criteri.
    3.  **Definizione criteri**: selezionare la definizione dei criteri da applicare all'ambito con esclusioni. Digitare "Web Application Firewall" nella barra di ricerca per scegliere il criterio di Azure per il Web Application Firewall pertinente.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Scheda nozioni di base nella pagina Assegna criteri":::

5.  Selezionare la scheda **parametri** e aggiornare i parametri di assegnazione dei criteri. Per chiarire ulteriormente il comportamento del parametro, passare il puntatore del mouse sull'icona delle informazioni accanto al nome del parametro per ulteriori chiarimenti.

6.  Selezionare **Verifica + crea** per finalizzare l'assegnazione dei criteri. L'assegnazione dei criteri richiede circa 15 minuti fino a quando non è attiva per le nuove risorse.
