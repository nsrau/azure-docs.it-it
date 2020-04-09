---
title: Creare criteri WAF (Web Application Firewall) per il gateway applicazione
description: Informazioni su come creare criteri di Web Application Firewall per il gateway applicazione.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: e3738da806ff36cdb7e8d561b88a457a5264eb76
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886926"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Creare criteri Di Web Application Firewall per il gateway applicazione

L'associazione di criteri WAF ai listener consente a più siti dietro un singolo WAF di essere protetti da criteri diversi. Ad esempio, se sono presenti cinque siti dietro il WAF, è possibile disporre di cinque criteri WAF separati (uno per ogni listener) per personalizzare le esclusioni, le regole personalizzate e i set di regole gestite per un sito senza influire sulle altre quattro. Se si desidera applicare un singolo criterio a tutti i siti, è sufficiente associare il criterio al gateway applicazione, anziché ai singoli listener, per applicarlo globalmente. I criteri possono essere applicati anche a una regola di routing basata su percorso. 

È possibile creare tutte le politiche desiderate. Una volta creato, un criterio deve essere associato a un gateway applicazione per essere attivo, ma può essere associato a qualsiasi combinazione di gateway applicazione e listener. 

Se al gateway applicazione è applicato un criterio e quindi si applicano criteri diversi a un listener in tale gateway applicazione, i criteri del listener avranno effetto, ma solo per i listener a cui sono assegnati. Il criterio del gateway applicazione si applica comunque a tutti gli altri listener a cui non è assegnato un criterio specifico. 

   > [!NOTE]
   > I criteri di WAF per sito e per URI sono disponibili in anteprima pubblica. Questa funzionalità è quindi soggetta alle condizioni per l'utilizzo supplementari di Microsoft. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
   > [!NOTE]
   > Una volta che un criterio firewall è associato a un WAF, deve essere sempre presente un criterio associato a tale WAF. È possibile sovrascrivere tale criterio, ma la dissociazione di un criterio dal WAF non è completamente supportata. 

Tutte le nuove impostazioni WAF di Web Application Firewall (regole personalizzate, configurazioni di rulset gestite, esclusioni e così via) risiedono all'interno di un criterio WAF. Se si dispone di un WAF esistente, queste impostazioni potrebbero essere ancora presenti nella configurazione WAF. Per istruzioni su come passare al nuovo criterio WAF, vedere Eseguire la migrazione della [configurazione WAF a un criterio WAF](#migrate) più avanti in questo articolo. 

## <a name="create-a-policy"></a>Creare criteri

Creare innanzitutto criteri WAF di base con un set di regole predefinito gestito tramite il portale di Azure.First, create a basic WAF policy with a managed Default Rule Set (DRS) using the Azure portal.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa**. Cercare **WAF**, selezionare **Web Application Firewall**, quindi selezionare **Create**.
2. Nella pagina **Crea criteri WAF,** scheda **Nozioni di base,** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le impostazioni rimanenti e quindi selezionare **Revisione e creazione:**

   |Impostazione  |valore  |
   |---------|---------|
   |Politica per     |WAF regionale (Gateway applicazione)|
   |Subscription     |Selezionare il nome dell'abbonamento|
   |Resource group     |Selezionare un gruppo di risorse|
   |Nome criteri     |Digitare un nome univoco per i criteri WAF.|
3. Nella scheda **Associazione** immettere una delle impostazioni seguenti, quindi selezionare **Aggiungi:**

   |Impostazione  |valore  |
   |---------|---------|
   |Associa gateway applicazione     |Selezionare il nome del profilo del gateway applicazione.|
   |Listener associati     |Selezionare il nome del listener del gateway applicazione, quindi selezionare **Aggiungi**.|

   > [!NOTE]
   > Se si assegna un criterio al gateway applicazione (o listener) che dispone già di un criterio, il criterio originale viene sovrascritto e sostituito dal nuovo criterio.
4. Selezionare **Rivedi e crea** e quindi **Crea**.

   ![Nozioni di base sulle politiche WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Configurare le regole WAF (facoltativo)

Quando si crea un criterio WAF, per impostazione predefinita è in modalità *di rilevamento.* In modalità di rilevamento, WAF non blocca le richieste. Al contrario, le regole WAF corrispondenti vengono registrate nei registri WAF. Per visualizzare WAF in azione, è possibile modificare le impostazioni della modalità in *Prevenzione*. In modalità Prevenzione, le regole di corrispondenza definite nel set di regole CRS selezionato vengono bloccate e/o registrate nei registri WAF.

## <a name="managed-rules"></a>Regole gestite

Le regole OWASP gestite da Azure sono abilitate per impostazione predefinita. Per disabilitare una singola regola all'interno di un gruppo di regole, espandere le regole all'interno di tale gruppo di regole, selezionare la casella di controllo davanti al numero di regola e selezionare **Disabilita** nella scheda precedente.

[![Regole](../media/create-waf-policy-ag/managed-rules.png) gestite](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Regole personalizzate

Per creare una regola personalizzata, selezionare **Aggiungi regola personalizzata** nella scheda **Regole personalizzate.** Verrà visualizzata la pagina di configurazione della regola personalizzata. Nella schermata seguente viene illustrata una regola personalizzata di esempio configurata per bloccare una richiesta se la stringa di query contiene il *blocco di*testo .

[![Modifica regola](../media/create-waf-policy-ag/edit-custom-rule.png) personalizzata](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Eseguire la migrazione della configurazione WAF a un criterio WAF

Se si dispone di un WAF esistente, è possibile che alcune modifiche nel portale. In primo luogo è necessario identificare il tipo di criterio che hai abilitato sul vostro WAF. Ci sono tre stati potenziali:

- Nessuna politica WAF
- Criterio solo regole personalizzate
- Criterio di WAF

Puoi dire in quale stato si trova il tuo WAF guardandolo nel portale. Se le impostazioni WAF sono visibili e possono essere modificate dalla visualizzazione Gateway applicazione, il WAF è nello stato 1.

[![Configurazione](../media/create-waf-policy-ag/waf-configure.png) WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Se si seleziona **Firewall applicazione Web** che mostra un criterio associato, il WAF è nello stato 2 o 3. Dopo lo spostamento al criterio, se vengono visualizzate **solo** le regole personalizzate e i gateway applicazione associati, si tratta solo di criteri solo regole personalizzate.

![Regole personalizzate di WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Se vengono visualizzate anche le impostazioni dei criteri e le regole gestite, si tratta di un criterio Firewall applicazione Web completo. 

![Impostazioni dei criteri WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Eseguire la migrazione ai criteri WAFMigrate to WAF Policy

Se si dispone di un criterio WAF solo regole personalizzate, è possibile passare al nuovo criterio WAF. In futuro, i criteri firewall supporteranno le impostazioni dei criteri WAF, i set di regole gestite, le esclusioni e i gruppi di regole disabilitati. Essenzialmente, tutte le configurazioni WAF che sono state eseguite in precedenza all'interno del gateway applicazione vengono ora eseguite tramite il criterio WAF. 

Le modifiche alla regola personalizzata solo i criteri WAF sono disabilitate. Per modificare le impostazioni WAF, ad esempio la disabilitazione delle regole, l'aggiunta di esclusioni e così via, è necessario eseguire la migrazione a una nuova risorsa dei criteri firewall di primo livello.

A tale scopo, creare un *criterio Firewall applicazione Web* e associarlo ai gateway applicazione e ai listener di scelta. Questo nuovo criterio **deve** essere esattamente lo stesso della configurazione WAF corrente, il che significa che ogni regola personalizzata, esclusione, regola disabilitata e così via deve essere copiata nel nuovo criterio che si sta creando. Dopo aver associato un criterio al gateway applicazione, è possibile continuare ad apportare modifiche alle regole e alle impostazioni WAF. È anche possibile eseguire questa operazione con Azure PowerShell.You can also do this with Azure PowerShell. Per ulteriori informazioni, vedere [Associare un criterio WAF a un gateway applicazione esistente.](associate-waf-policy-existing-gateway.md)

Facoltativamente, è possibile utilizzare uno script di migrazione per eseguire la migrazione a un criterio WAF. Per altre informazioni, vedere [Eseguire la migrazione dei criteri di Firewall applicazione Web tramite Azure PowerShell.For](migrate-policy.md)more information, see Migrate Web Application Firewall policies using Azure PowerShell.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui gruppi di [regole CRS di Web Application Firewall e](application-gateway-crs-rulegroups-rules.md)sulle regole .
