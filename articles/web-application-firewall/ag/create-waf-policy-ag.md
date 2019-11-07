---
title: Creare criteri di Web Application Firewall (WAF) per il gateway applicazione
description: Informazioni su come creare criteri del Web Application Firewall per il gateway applicazione.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 10/26/2019
ms.author: victorh
ms.openlocfilehash: 31a5ad92942b40e42b66e404df2d09cd8158f7a2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606476"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Creare criteri del Web Application Firewall per il gateway applicazione

L'associazione di un criterio WAF ai listener consente a più siti dietro un singolo WAF di essere protetti da criteri diversi. Ad esempio, se sono presenti cinque siti dietro la WAF, è possibile avere cinque criteri WAF distinti (uno per ogni listener) per personalizzare le esclusioni, le regole personalizzate e i RuleSet gestiti per un sito senza influire sugli altri quattro. Se si vuole applicare un singolo criterio a tutti i siti, è sufficiente associare il criterio al gateway applicazione, anziché ai singoli listener, per applicarlo a livello globale. I criteri possono essere applicati anche a una regola di routing basata sul percorso. 

   > [!NOTE]
   > Il criterio WAF per sito è disponibile negli Stati Uniti centro-meridionali ed Europa settentrionale. Per accedervi nel portale, usare [questo collegamento](https://aka.ms/AppgwwafWithAllFeatureFlags) fino a quando non diventa disponibile per tutti.  

È possibile creare tutti i criteri desiderati. Dopo aver creato un criterio, è necessario associarlo a un gateway applicazione per renderlo effettivo, ma può essere associato a qualsiasi combinazione di gateway applicazione e listener. 

Se per il gateway applicazione è stato applicato un criterio e si applica un criterio diverso a un listener nel gateway applicazione, i criteri del listener saranno effettivi, ma solo per i listener a cui sono assegnati. Il criterio del gateway applicazione si applica comunque a tutti gli altri listener a cui non è assegnato un criterio specifico. 

   > [!NOTE]
   > I criteri WAF per sito e per URI sono disponibili in anteprima pubblica. Questo significa che questa funzionalità è soggetta alle condizioni per l'utilizzo aggiuntive di Microsoft. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tutte le nuove impostazioni WAF del Web Application Firewall (regole personalizzate, configurazioni rulset gestite, esclusioni e così via) risiedono all'interno di un criterio WAF. Se si dispone di un WAF esistente, queste impostazioni potrebbero ancora esistere nella configurazione di WAF. Per istruzioni su come passare al nuovo criterio WAF, vedere eseguire la [migrazione della configurazione di WAF a un criterio WAF](#migrate) più avanti in questo articolo. 

## <a name="create-a-policy"></a>Creare un criterio

Per prima cosa, creare un criterio WAF di base con un set di regole predefinite gestito usando il portale di Azure.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa**. Cercare **WAF**, selezionare **Web Application Firewall**, quindi selezionare **Crea**.
2. Nella scheda **nozioni di base** della pagina **creare un criterio WAF** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le restanti impostazioni e quindi selezionare **Verifica + crea**:

   |Impostazione  |Valore  |
   |---------|---------|
   |Criteri per     |WAF a livello di area (gateway applicazione)|
   |Sottoscrizione     |Selezionare il nome della sottoscrizione|
   |Resource group     |Selezionare un gruppo di risorse|
   |Nome criterio     |Digitare un nome univoco per i criteri di WAF.|
3. Nella scheda **associazione** immettere una delle impostazioni seguenti e quindi selezionare **Aggiungi**:

   |Impostazione  |Valore  |
   |---------|---------|
   |Associare il gateway applicazione     |Selezionare il nome del profilo del gateway applicazione.|
   |Associare listener     |Selezionare il nome del listener del gateway applicazione e quindi selezionare **Aggiungi**.|

   > [!NOTE]
   > Se si assegna un criterio al gateway applicazione (o listener) che dispone già di un criterio, il criterio originale viene sovrascritto e sostituito con il nuovo criterio.
4. Selezionare **Verifica + crea**, quindi selezionare **Crea**.

   ![Nozioni fondamentali sui criteri WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Configurare le regole di WAF (facoltativo)

Quando si crea un criterio WAF, per impostazione predefinita si trova in modalità di *rilevamento* . In modalità di rilevamento, WAF non blocca alcuna richiesta. Al contrario, le regole WAF corrispondenti vengono registrate nei log di WAF. Per visualizzare WAF in azione, è possibile modificare le impostazioni della modalità in *prevenzione*. In modalità di prevenzione, le regole di corrispondenza definite nel set di regole CRS selezionato sono bloccate e/o registrate nei registri WAF.

## <a name="managed-rules"></a>Regole gestite

Le regole OWASP gestite da Azure sono abilitate per impostazione predefinita. Per disabilitare una singola regola in un gruppo di regole, espandere le regole all'interno di tale gruppo, selezionare la casella di controllo davanti al numero della regola e selezionare **Disabilita** nella scheda sopra.

![Regole gestite](../media/create-waf-policy-ag/managed-rules.png)

## <a name="custom-rules"></a>Regole personalizzate

Per creare una regola personalizzata, selezionare **Aggiungi regola personalizzata** nella scheda **regole personalizzate** . Verrà visualizzata la pagina Configurazione regola personalizzata. La schermata seguente mostra una regola personalizzata di esempio configurata per bloccare una richiesta se la stringa di query contiene il testo *blockme*.

![Modifica regola personalizzata](../media/create-waf-policy-ag/edit-custom-rule.png)

## <a name="migrate"></a>Eseguire la migrazione della configurazione di WAF a un criterio WAF

Se si dispone di un WAF esistente, è possibile che siano state apportate alcune modifiche al portale. Prima di tutto è necessario identificare il tipo di criteri abilitato per la WAF. Esistono tre possibili stati:

- Nessun criterio WAF
- Criterio solo per le regole personalizzate
- Criteri di WAF

È possibile indicare lo stato in cui si trova il WAF nel portale. Se le impostazioni di WAF sono visibili e possono essere modificate nella visualizzazione del gateway applicazione, il WAF è nello stato 1.

![Configurazione di WAF](../media/create-waf-policy-ag/waf-configure.png)

Se si seleziona **Web Application Firewall** e viene visualizzato un criterio associato, WAF è nello stato 2 o stato 3. Dopo aver esplorato i criteri, se vengono visualizzate **solo** le regole personalizzate e i gateway applicazione associati, si tratta di un criterio personalizzato solo per le regole.

![Regole personalizzate di WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Se vengono visualizzate anche le impostazioni dei criteri e le regole gestite, si tratta di un criterio del firewall applicazione Web completo. 

![Impostazioni dei criteri di WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Esegui la migrazione a criteri WAF

Se si dispone di un criterio WAF solo regole personalizzate, è possibile passare al nuovo criterio WAF. In futuro, i criteri firewall supporteranno le impostazioni dei criteri WAF, i RuleSet gestiti, le esclusioni e i gruppi di regole disabilitati. In pratica, tutte le configurazioni WAF eseguite in precedenza all'interno del gateway applicazione vengono ora eseguite tramite il criterio WAF. 

Le modifiche alla regola personalizzata solo i criteri WAF sono disabilitati. Per modificare le impostazioni di WAF, ad esempio la disabilitazione delle regole, l'aggiunta di esclusioni e così via, è necessario eseguire la migrazione a una nuova risorsa di criteri del firewall di livello superiore.

A tale scopo, creare un *criterio di Web Application Firewall* e associarlo ai gateway applicazione e ai listener scelti. Questo nuovo criterio **deve** corrispondere esattamente alla configurazione WAF corrente, ovvero ogni regola personalizzata, esclusione, regola disabilitata e così via. deve essere copiato nel nuovo criterio che si sta creando. Dopo aver creato un criterio associato al gateway applicazione, è possibile continuare a modificare le regole e le impostazioni di WAF. 

Questa operazione può essere eseguita anche con Azure PowerShell. Per altre informazioni, vedere [associare un criterio WAF a un gateway applicazione esistente](associate-waf-policy-existing-gateway.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [regole e gruppi di regole CRS del Web Application Firewall](application-gateway-crs-rulegroups-rules.md).
