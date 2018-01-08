---
title: Personalizzazione delle configurazioni di protezione del sistema operativo nel Centro protezione di Azure [anteprima] | Documenti Microsoft
description: In questo articolo viene illustrato come personalizzare le valutazioni di centro di sicurezza
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 2fa63515d290e6700fbe4a90ae509f4635b19f29
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Personalizzazione delle configurazioni di protezione del sistema operativo nel Centro protezione di Azure [anteprima]

Informazioni su come personalizzare le valutazioni della configurazione di sicurezza del sistema operativo nel Centro protezione di Azure tramite questa procedura dettagliata.

## <a name="what-are-os-security-configurations"></a>Quali sono le configurazioni di sicurezza del sistema operativo?

Configurazioni di sicurezza monitoraggi Centro sicurezza di Azure utilizzando un set di regole consigliate più di 150 per la protezione avanzata del sistema operativo, incluse regole correlate a firewall, controllo, i criteri password e così via. Se viene trovato un computer dispone di una configurazione vulnerabile, viene generata una raccomandazione di sicurezza.

Personalizzazione delle regole consente alle organizzazioni di controllo quali opzioni di configurazione sono più appropriate per il proprio ambiente. Questa funzionalità consente agli utenti di impostare un criterio personalizzato di valutazione e applicarlo a tutti i computer applicabili nella sottoscrizione.

> [!NOTE]
> - Personalizzazione di configurazione della sicurezza del sistema operativo è attualmente disponibile per Windows Server 2008, 2008R2, 2012, 2012 R2 solo nei sistemi operativi.
- La configurazione si applica a tutte le macchine virtuali e i computer connessi a tutte le aree di lavoro nella sottoscrizione selezionata.
- Personalizzazione di configurazione della sicurezza del sistema operativo è disponibile solo nel livello Standard del Centro sicurezza PC.
>
>

Come personalizzare le regole di configurazione della sicurezza del sistema operativo?

Abilitazione e disabilitazione di una regola specifica, modifica l'impostazione desiderata per una regola esistente, e aggiungendo una nuova regola in base ai tipi di regola supportati (del Registro di sistema, i criteri di controllo e criteri di sicurezza), è possibile personalizzare le regole di configurazione della sicurezza del sistema operativo. Attualmente, l'impostazione desiderata deve essere un valore esatto.

Nuove regole devono essere nello stesso formato e struttura come altre regole esistenti dello stesso tipo.

> [!NOTE]
> Per personalizzare le configurazioni di sicurezza del sistema operativo, è necessario essere assegnato il ruolo di proprietario della sottoscrizione, sottoscrizione collaboratore o amministratore della sicurezza.
>
>

## <a name="customize-security-configuration"></a>Personalizzazione della configurazione di sicurezza

Per personalizzare il valore predefinito di configurazione della sicurezza del sistema operativo del Centro sicurezza PC:

1.  Aprire il dashboard del **Centro sicurezza**.

2.  Selezionare il menu principale di Centro sicurezza PC, **criteri di sicurezza**.  **Centro sicurezza PC - criteri di sicurezza** apre.

3.  Selezionare la sottoscrizione che si desidera eseguire la personalizzazione per.

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. In **componenti criteri**selezionare **modificare le configurazioni di sicurezza**.

6.  **Modificare le configurazioni di sicurezza** apre. Seguire i passaggi evidenziati nella schermata per scaricare, modificare e caricare il file modificato.

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > Per impostazione predefinita, il file di configurazione che si scarica è *json* formato. Per istruzioni su come modificare questo file, passare a [personalizzare il file di configurazione](#customize-the-configuration-file).
  >

7. Dopo il salvataggio del file, la configurazione viene applicata a tutte le macchine virtuali e i computer connessi a tutte le aree di lavoro nella sottoscrizione selezionata. Questo processo potrebbe richiedere alcuni minuti, in genere alcuni minuti, ma può può richiedere più tempo poiché dipende la dimensione dell'infrastruttura. Selezionare **salvare** per eseguire il commit della modifica, in caso contrario il criterio non viene archiviato.

    ![](media/security-center-customize-os-security-config/save-successfully.png)

In qualsiasi momento, è possibile reimpostare la configurazione corrente di criteri per lo stato dei criteri predefinito selezionando il **reimpostare** opzione **le regole di configurazione di sicurezza del sistema operativo modifica**. Se si sceglie questa opzione, verrà visualizzato il seguente avviso popup. Selezionare **Sì** per confermare.

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personalizzare il file di configurazione

Nel file di personalizzazione ogni versione del sistema operativo supportato è un set di regole (ruleset). Ogni set di regole con il proprio nome e l'ID univoco, come illustrato nell'esempio seguente:

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Questo file è stato modificato in Visual Studio, ma è inoltre possibile utilizzare il blocco note, purché si disponga del plug-in Visualizzatore JSON è installato.
>
>

Quando si modifica questo file, è possibile modificare una regola o tutti i parametri. Ogni set di regole include un *regole* sezione che contiene le regole, suddivise in 3 categorie di regole: Registro di sistema, i criteri di controllo e criteri di sicurezza, come illustrato di seguito:

![](media/security-center-customize-os-security-config/rules-section.png)

Ogni categoria dispone di un proprio set di attributi. Per le regole esistenti, è possibile apportare modifiche in quelli seguenti:

- expectedValue: tipo di dati di campo di questo attributo deve corrispondere i valori supportati per ogni tipo di regola, ad esempio:

  - baselineRegistryRules: il valore deve corrispondere [regValueType] (https://msdn.microsoft.com/library/windows/desktop/ms724884 (v=vs.85) definite in tale regola.

  - baselineAuditPolicyRules: il valore deve essere un valore stringa, uno dei seguenti:

    - Operazioni riuscite e operazioni non riuscite

    - Success

  - baselineSecurityPolicyRules: il valore deve essere un valore stringa, uno dei seguenti:

    - "Nessuno"

    - Elenco di gruppi di utenti è consentito, ad esempio: "Amministratori, Backup Operators"

-   stato: stringa che può contenere le opzioni "Enabled" o "Disabilitate". Per questa versione di anteprima privata, la stringa è tra maiuscole e minuscole.

Questi sono gli unici campi che possono essere configurati. In caso di violazione il formato di file o le dimensioni, non sarà in grado di salvare le modifiche. Il seguente messaggio di errore si verifica quando il file non può essere elaborato:

![](media/security-center-customize-os-security-config/invalid-json.png)

Vedere [codici di errore](#error-codes) per elenco di potenziali errori.

Di seguito sono riportati esempi di queste regole e gli attributi (in grassetto) che possono essere modificati:

**Sezione regole:** baselineRegistryRules
```
{

    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "**expectedValue**": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "**state**": "Disabled"

}
```

**Sezione regole:** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"**expectedValue**": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"**state**": "Enabled"
},
```

**Sezioni di regole:** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"**expectedValue**": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft", "**state**": "Enabled"
},
```

Esistono alcune regole che vengono duplicati per i diversi tipi di sistemi operativi. Le regole duplicate hanno stesso 'originalId'.

## <a name="adding-a-new-custom-rule"></a>Aggiunta di una nuova regola personalizzata

È anche possibile creare una nuova regola. Prima di creare una nuova regola, tenere presente le restrizioni seguenti:

-   Versione dello schema, *baselineId* e *baselineName* non può essere modificato.

-   Impossibile rimuovere il set di regole.

-   Impossibile aggiungere il set di regole.

-   Il numero massimo di regole consentite (incluse le regole predefinite) è 1000 regole.

Nuove regole personalizzate sono contrassegnate con una nuova origine personalizzata (! = "Microsoft"). Il *ruleId* campo può essere null o vuoto. Se è vuota, viene generata automaticamente una Microsoft. Se non è vuota, è necessario che un GUID valido univoco in tutte le regole (predefinite e personalizzate). Esaminare i vincoli sotto i campi fondamentali:

-   *originalId* -può essere null o vuoto. Se *originalId* è non vuoto, deve essere un GUID valido.

-   *cceId* -può essere null o vuoto. Se *cceId* è non vuoto, deve essere univoco.

-   *ruleType* : uno di: Registro di sistema, AuditPolicy o SecurityPolicy.

-   *Gravità* : uno di: sconosciuto, critico, avviso o informativo.

-   *analyzeOperation* -deve essere uguale a.

-   *auditPolicyId* -deve essere un GUID valido.

-   *regValueType* -deve essere uno di: Int, Long, stringa o MultipleString.

> [!NOTE]
> Deve essere hive *LocalMachine*.
>
>

Esempio di una nuova regola personalizzata:

**Registro di sistema**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
   }
```
**Criteri di sicurezza**:
```
{

   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Criteri di controllo:**
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Errori di caricamento file

Se il file di configurazione inviata non è valido a causa di errori in valori o formattazione, viene visualizzato un errore. È possibile scaricare il report csv errori dettagliati per risolvere e correggere gli errori prima di ritentare l'invio di un file di configurazione corretto.

![](media/security-center-customize-os-security-config/invalid-configuration.png)

Esempio di file di errori:

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Codici di errore

Nell'elenco seguente contiene tutti gli errori potenziali:

| **Error (Errore) (Error (Errore)e)**                                | **Descrizione**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Trovare la proprietà 'schemaVersion' non valido o vuoto. Il valore deve essere impostato su '{0}'.                                                         |
| BaselineInvalidStringError               | Proprietà '{0}' non può contenere '\\n'.                                                                                                         |
| BaselineNullRuleError                    | Elenco di regole di configurazione della linea di base contiene una regola con valore 'null'.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID '{0}' non è univoco.                                                                                                                  |
| BaselineRuleEmptyProperty                | Proprietà: '{0}' è mancante o non valido.                                                                                                       |
| BaselineRuleIdNotInDefault               | La regola ha una proprietà di origine 'Microsoft' ma non è stata trovata nel set di regole predefinite Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | Id regola non è univoco.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Trovare la proprietà '{0}' non valida. Il valore non è un Guid valido.                                                                             |
| BaselineRuleInvalidHive                  | Hive deve essere LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Il nome della regola non è univoco.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | La regola non è stata trovata nella configurazione di nuovo. Regola non può essere eliminata.                                                                     |
| BaselineRuleNotFoundError                | La regola non trovata in set di regole di base.                                                                                        |
| BaselineRuleNotInPlace                   | La regola corrisponde a una regola predefinita con il tipo {0} e incluso nell'elenco di \\{1 \\}.                                                                       |
| BaselineRulePropertyTooLong              | Proprietà: '{0}' è troppo lungo. La lunghezza massima consentita: \\{1 \\}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Il valore previsto '{0}' non corrisponde al tipo di valore del Registro di sistema è definito.                                                              |
| BaselineRulesetAdded                     | Set di regole con id '{0}' non è stato trovato nella configurazione predefinita. Set di regole non può essere aggiunto.                                               |
| BaselineRulesetIdMustBeUnique            | Il set di regole di base specificato '{0}' deve essere univoco.                                                                                           |
| BaselineRulesetNotFound                  | Set di regole con id '{0}' e nome '\\{1 \\}' non è stato trovato nella configurazione specificata. Impossibile eliminare il set di regole.                                |
| BaselineRuleSourceNotMatch               | La regola con id '{0}' è già definita.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Il tipo di regola predefinita è '{0}'.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | È il tipo effettivo della regola: {0}, ma ruleType è di proprietà: \\{1 \\}.                                                                          |
| BaselineRuleUnpermittedChangesError      | Solo le proprietà 'expectedValue' e 'state' possono essere modificati.                                                                       |
| BaselineTooManyRules                     | Le regole personalizzate numero massimo consentito è {0} regole. La configurazione specificata contiene le regole di \\{1 \\}. (le regole predefinite {2} + 3 \} di regole personalizzate. |
| ErrorNoConfigurationStatus               | Nessuno stato di configurazione è stato trovato. Riportare lo stato della configurazione desiderata - 'Default' o 'Custom'.                                    |
| ErrorNonEmptyRulesetOnDefault            | Lo stato di configurazione è impostato sul valore predefinito. Elenco BaselineRulesets deve essere null o vuoto.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Lo stato di configurazione specificato è 'Custom', ma la proprietà 'baselineRulesets' è null o vuoto.                                             |
| ErrorParsingBaselineConfig               | La configurazione specificata non è valida. Uno o più dei valori definiti hanno un valore null o un tipo non valido.                                  |
| ErrorParsingIsDefaultProperty            | Il valore specificato 'configurationStatus' '{0}' non è valido. Il valore può essere solo 'Default' o 'Custom'.                                         |
| InCompatibleViewVersion                  | Visualizza la versione: {0} non è supportato in questo tipo di area di lavoro.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Impossibile trovare la configurazione di base specificato con uno o più errori di convalida di tipo.                                                          |
| ViewConversionError                      | La vista è versione area di lavoro supportato. Visualizzare una conversione non riuscita: {0}                                                                 |

Se non si dispone di autorizzazioni sufficienti, è possibile che si verifichi un errore generale:

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come personalizzare le valutazioni della configurazione di sicurezza del sistema operativo del Centro sicurezza PC. Per ulteriori informazioni sulle regole di configurazione e monitoraggio e aggiornamento, vedere:

- [Centro sicurezza PC gli identificatori di configurazione comuni e le regole della linea di base](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci per le regole di configurazione. Per altre informazioni, vedere la pagina relativa alla enumerazione [CCE](https://nvd.nist.gov/config/cce/index) .
- [Monitorare e aggiornare le configurazioni di sicurezza](security-center-remediate-os-vulnerabilities.md) viene illustrato come risolvere le vulnerabilità durante la configurazione del sistema operativo corrispondono alle regole di configurazione consigliata per la protezione.
