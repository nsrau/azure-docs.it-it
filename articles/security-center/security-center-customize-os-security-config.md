---
title: Personalizzare le configurazioni di sicurezza del sistema operativo nel Centro sicurezza di Azure (anteprima) | Microsoft Docs
description: Questo articolo illustra come personalizzare le valutazioni del centro sicurezza
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
ms.date: 01/25/2018
ms.author: terrylan
ms.openlocfilehash: f12441a960db9f1c45bca2a5b95f3669923c7e3d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Personalizzare le configurazioni di sicurezza del sistema operativo nel Centro sicurezza di Azure (anteprima)

Questa procedura dettagliata illustra come personalizzare le valutazioni delle configurazioni di sicurezza del sistema operativo nel Centro sicurezza di Azure.

## <a name="what-are-os-security-configurations"></a>Informazioni sulle configurazioni di sicurezza del sistema operativo

Il Centro sicurezza di Azure monitora le configurazioni di sicurezza applicando un set di [più di 150 regole consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) per la protezione avanzata del sistema operativo, incluse regole relative a firewall, controllo, criteri di gestione delle password e altro ancora. Se in un computer viene trovata una configurazione vulnerabile, il Centro sicurezza genera una raccomandazione di sicurezza.

Personalizzando le regole, le organizzazioni possono controllare quali opzioni di configurazione sono più appropriate per il proprio ambiente. È possibile impostare un criterio personalizzato di valutazione e quindi applicarlo in tutti i computer applicabili nella sottoscrizione.

> [!NOTE]
> - La personalizzazione della configurazione di sicurezza del sistema operativo è attualmente disponibile solo per i sistemi operativi Windows Server versioni 2008, 2008 R2, 2012 e 2012 R2.
> - La configurazione si applica a tutte le macchine virtuali e a tutti i computer connessi a tutte le aree di lavoro nella sottoscrizione selezionata.
> - La personalizzazione della configurazione di sicurezza del sistema operativo è disponibile solo nel livello Standard del Centro sicurezza.
>
>

È possibile personalizzare le regole di configurazione di sicurezza del sistema operativo abilitando e disabilitando una regola specifica, modificando l'impostazione desiderata per una regola esistente o aggiungendo una nuova regola basata sui tipi di regole supportati (Registro di sistema, criteri di controllo e criteri di sicurezza). Attualmente, l'impostazione desiderata deve essere un valore esatto.

Le nuove regole devono essere nello stesso formato e nella stessa struttura delle altre regole esistenti dello stesso tipo.

> [!NOTE]
> Per personalizzare le configurazioni di sicurezza del sistema operativo, è necessario avere il ruolo di *proprietario della sottoscrizione*, *collaboratore alla sottoscrizione* o *amministratore della sicurezza*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Personalizzare la configurazione di sicurezza del sistema operativo predefinita

Per personalizzare la configurazione di sicurezza predefinita del sistema operativo nel Centro sicurezza, seguire questa procedura:

1.  Aprire il dashboard **Centro sicurezza**.

2.  Nel riquadro di sinistra selezionare **Criteri di sicurezza**.  
    Viene aperta la finestra **Centro sicurezza - Criteri di sicurezza**.

    ![Elenco Criteri di sicurezza](media/security-center-customize-os-security-config/open-security-policy.png)

3.  Selezionare la sottoscrizione per cui si vuole eseguire la personalizzazione.

4. In **Componenti dei criteri** selezionare **Modifica le configurazioni della sicurezza**.  
    Viene aperta la finestra **Modifica le configurazioni della sicurezza**.

    ![Finestra "Modifica le configurazioni della sicurezza"](media/security-center-customize-os-security-config/blade.png)

5. Nel riquadro di destra seguire i passaggi per il download, la modifica e il caricamento del file modificato.

   > [!NOTE]
   > Per impostazione predefinita, il file di configurazione scaricato è in formato *json*. Per istruzioni sulla modifica di questo file, vedere [Personalizzare il file di configurazione](#customize-the-configuration-file).
   >

   Dopo avere salvato il file, la configurazione viene applicata a tutte le macchine virtuali e a tutti i computer connessi a tutte le aree di lavoro nella sottoscrizione. Il processo in genere richiede alcuni minuti, ma possono servirne di più, a seconda delle dimensioni dell'infrastruttura.

6. Per eseguire il commit della modifica, selezionare **Salva**. In caso contrario, i criteri non vengono archiviati.

    ![Pulsante Salva](media/security-center-customize-os-security-config/save-successfully.png)

In qualsiasi momento, è possibile ripristinare lo stato predefinito della configurazione dei criteri corrente. A questo scopo, nella finestra **Modifica le regole di configurazione della sicurezza del sistema operativo** selezionare **Reimposta**. Confermare questa opzione selezionando **Sì** nella finestra popup di conferma.

![Finestra di conferma della reimpostazione](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personalizzare il file di configurazione

Nel file di personalizzazione ogni versione del sistema operativo supportata ha un set di regole. Ogni set di regole ha il proprio nome e ID univoco, come illustrato nell'esempio seguente:

![Nome e ID del set di regole](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Questo file di esempio è stato modificato in Visual Studio, ma è anche possibile usare il Blocco note, se il plug-in Visualizzatore JSON è installato.
>
>

Quando si modifica il file di personalizzazione, è possibile modificare una regola o tutte. Ogni set di regole include una sezione *rules* suddivisa in tre categorie: Registro di sistema, criteri di controllo e criteri di sicurezza, come illustrato qui:

![Tre categorie del set di regole](media/security-center-customize-os-security-config/rules-section.png)

Ogni categoria ha il proprio set di attributi. È possibile modificare gli attributi seguenti:

- **expectedValue**: il tipo di dati del campo di questo attributo deve corrispondere ai valori supportati per ogni *tipo di regola*, ad esempio:

  - **baselineRegistryRules**: il valore deve corrispondere all'elemento [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) definito in tale regola.

  - **baselineAuditPolicyRules**: usare uno dei valori stringa seguenti:

    - *Success and Failure*

    - *Success*

  - **baselineSecurityPolicyRules**: usare uno dei valori stringa seguenti:

    - *No one*

    - Elenco di gruppi utenti consentiti, ad esempio: *Administrators*, *Backup Operators*

-   **state**: la stringa può contenere le opzioni *Disabled* o *Enabled*. In questa versione di anteprima privata la stringa fa distinzione tra maiuscole e minuscole.

Questi sono gli unici campi che possono essere configurati. In caso di violazione del formato o delle dimensioni del file, non sarà possibile salvare le modifiche. Il messaggio di errore seguente viene visualizzato quando il file non può essere elaborato:

![Messaggio di errore di configurazione della sicurezza](media/security-center-customize-os-security-config/invalid-json.png)

Per un elenco di altri potenziali errori, vedere [Codici di errore](#error-codes).

Le tre sezioni seguenti contengono esempi delle regole precedenti. Gli attributi *expectedValue* e *state* non possono essere modificati.

**baselineRegistryRules**
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
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

Alcune regole vengono duplicate per i diversi tipi di sistemi operativi. Le regole duplicate hanno lo stesso attributo *originalId*.

## <a name="create-custom-rules"></a>Creare regole personalizzate

È anche possibile creare nuove regole. Prima di creare una nuova regola, tenere presenti le restrizioni seguenti:

-   La versione dello schema, *baselineId* e *baselineName* non possono essere modificati.

-   Il set di regole non può essere rimosso.

-   Il set di regole non può essere aggiunto.

-   Il numero massimo di regole consentite (incluse le regole predefinite) è 1000.

Le nuove regole personalizzate sono contrassegnate con una nuova origine personalizzata (!= "Microsoft"). Il campo *ruleId* può essere null o vuoto. Se è vuoto, Microsoft ne genera uno. Se non è vuoto, è necessario avere un GUID valido univoco in tutte le regole (predefinite e personalizzate). Esaminare i vincoli seguenti per i campi principali:

-   **originalId**: può essere null o vuoto. Se *originalId* non è vuoto, deve essere un GUID valido.

-   **cceId**: può essere null o vuoto. Se *cceId* non è vuoto, deve essere univoco.

-   **ruleType**: selezionare Registry, AuditPolicy o SecurityPolicy.

-   **Severity**: selezionare Unknown, Critical, Warning o Informational.

-   **analyzeOperation**: deve essere *Equals*.

-   **auditPolicyId**: deve essere un GUID valido.

-   **regValueType**: selezionare Int, Long, String o MultipleString.

> [!NOTE]
> L'hive deve essere *LocalMachine*.
>
>

Esempio di nuova regola personalizzata:

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
**Criteri di controllo**:
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

Se il file di configurazione inviato non è valido a causa di errori nei valori o nella formattazione, viene visualizzato un errore. È possibile scaricare un report dettagliato sugli errori con estensione csv per risolvere e correggere gli errori prima inviare di nuovo un file di configurazione corretto.

![Messaggio di errore "Non è stato possibile eseguire l'azione di salvataggio"](media/security-center-customize-os-security-config/invalid-configuration.png)

Esempio di file di errore:

![Esempio di file di errore](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Codici di errore

Tutti i potenziali errori sono elencati nella tabella seguente:

| **Error (Errore) (Error (Errore)e)**                                | **Descrizione**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | La proprietà *schemaVersion* non è valida o è vuota. Il valore deve essere impostato su *{0}*.                                                         |
| BaselineInvalidStringError               | La proprietà *{0}* non può contenere *\\n*.                                                                                                         |
| BaselineNullRuleError                    | L'elenco di regole di configurazione di base contiene una regola con il valore *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | L'ID CCE *{0}* non è univoco.                                                                                                                  |
| BaselineRuleEmptyProperty                | La proprietà *{0}* manca o non è valida.                                                                                                       |
| BaselineRuleIdNotInDefault               | La regola ha una proprietà di origine *Microsoft*, ma non è stata trovata nel set di regole predefinite Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | L'ID della regola non è univoco.                                                                                                                       |
| BaselineRuleInvalidGuid                  | La proprietà *{0}* non è valida. Il valore non è un GUID valido.                                                                             |
| BaselineRuleInvalidHive                  | L'hive deve essere LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Il nome della regola non è univoco.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | La regola non è stata trovata nella nuova configurazione. Non è possibile eliminare la regola.                                                                     |
| BaselineRuleNotFoundError                | La regola non è stata trovata nel set di regole di base predefinito.                                                                                        |
| BaselineRuleNotInPlace                   | La regola corrisponde a una regola predefinita con il tipo {0} ed è inclusa nell'elenco {1}.                                                                       |
| BaselineRulePropertyTooLong              | La proprietà *{0}* è troppo lunga. Lunghezza massima consentita: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Il valore previsto *{0}* non corrisponde al tipo valore del Registro di sistema definito.                                                              |
| BaselineRulesetAdded                     | Il set di regole con l'ID *{0}* non è stato trovato nella configurazione predefinita. Il set di regole non può essere aggiunto.                                               |
| BaselineRulesetIdMustBeUnique            | Il set di regole di base specificato *{0}* deve essere univoco.                                                                                           |
| BaselineRulesetNotFound                  | Il set di regole con ID *{0}* e nome *{1}* non è stato trovato nella configurazione specificata. Il set di regole non può essere eliminato.                                |
| BaselineRuleSourceNotMatch               | La regola con ID *{0}* è già definita.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Il tipo di regola predefinito è *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Il tipo effettivo della regola è *{0}*, ma la proprietà *ruleType* è *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | È consentito modificare solo le proprietà *expectedValue* e *state*.                                                                       |
| BaselineTooManyRules                     | Il numero massimo di regole personalizzate consentite è {0} regole. La configurazione specificata contiene {1} regole, {2} regole predefinite e {3} regole personalizzate. |
| ErrorNoConfigurationStatus               | Nessuno stato di configurazione è stato trovato. Impostare lo stato di configurazione desiderato: *Default* o *Custom*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Lo stato della configurazione è impostato sul valore predefinito. L'elenco *BaselineRulesets* deve essere null o vuoto.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Lo stato della configurazione specificato è *Custom*, ma la proprietà *BaselineRulesets* è null o vuota.                                             |
| ErrorParsingBaselineConfig               | La configurazione specificata non è valida. Uno o più valori definiti hanno un valore null o un tipo non valido.                                  |
| ErrorParsingIsDefaultProperty            | Il valore *configurationStatus* specificato *{0}* non è valido. Il valore può essere solo *Default* o *Custom*.                                         |
| InCompatibleViewVersion                  | La versione della visualizzazione *{0}* *non* è supportata in questo tipo di area di lavoro.                                                                                   |
| InvalidBaselineConfigurationGeneralError | La configurazione di base specificata contiene uno o più errori di convalida del tipo.                                                          |
| ViewConversionError                      | La versione della visualizzazione è precedente a quella supportata dall'area di lavoro. Conversione della visualizzazione non riuscita: {0}.                                                                 |

Se le autorizzazioni disponibili non sono sufficienti, potrebbe essere visualizzato un errore generale, come illustrato qui:

![Messaggio di errore "Non è stato possibile eseguire l'azione di salvataggio"](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha illustrato come personalizzare le valutazioni delle configurazioni di sicurezza del sistema operativo nel Centro sicurezza. Per altre informazioni sulle regole di configurazione e sulle correzioni, vedere:

- [Security Center common configuration identifiers and baseline rules (Regole di base e identificatori di configurazione comuni del Centro sicurezza)](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci alle regole di configurazione. Per altre informazioni, vedere [CCE](https://nvd.nist.gov/config/cce/index).
- Per risolvere le vulnerabilità quando la configurazione del sistema operativo non corrisponde alle regole di configurazione della sicurezza consigliate, vedere [Correggere le configurazioni di sicurezza](security-center-remediate-os-vulnerabilities.md).
