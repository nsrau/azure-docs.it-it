---
title: Domande frequenti-Azure Key Vault importazione di certificati
description: Risposte alle domande frequenti sull'importazione di certificati Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: d7d34b61e584b63c517b6c0f8af4cb4adcc7fefe
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289502"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Domande frequenti sull'importazione di certificati Azure Key Vault

Questo articolo fornisce le risposte alle domande frequenti sull'importazione di certificati Azure Key Vault.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Come è possibile importare un certificato in Azure Key Vault?

Per un'operazione di importazione di certificati, Azure Key Vault accetta due formati di file di certificato: PEM e PFX. Sebbene siano presenti file PEM con solo la parte pubblica, Key Vault richiede e accetta solo un file PEM o PFX con una chiave privata. Per ulteriori informazioni, vedere [importare un certificato in Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Dopo aver importato un certificato protetto da password per Key Vault e quindi averlo scaricato, perché non è possibile visualizzare la password associata?
    
Una volta che un certificato viene importato e protetto in Key Vault, la password associata non viene salvata. La password è obbligatoria solo una volta durante l'operazione di importazione. Si tratta di un problema di progettazione, ma è sempre possibile ottenere il certificato come segreto e convertirlo da Base64 a PFX aggiungendo la password tramite [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Come è possibile risolvere un errore di "parametro non valido"? Quali sono i formati di certificato supportati per l'importazione Key Vault?

Quando si importa un certificato, è necessario assicurarsi che la chiave sia inclusa nel file. Se una chiave privata è archiviata separatamente in un formato diverso, è necessario combinare la chiave con il certificato. Alcune autorità di certificazione (CAs) forniscono certificati in altri formati. Pertanto, prima di importare il certificato, verificare che sia in formato di file PEM o PFX e che la chiave usi la crittografia di Rivest-Shamir-Adleman (RSA) o la crittografia a curva ellittica (ECC). 

Per ulteriori informazioni, vedere requisiti dei [certificati](./certificate-scenarios.md#formats-of-import-we-support) e [requisiti chiave del certificato](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>È possibile importare un certificato usando un modello ARM?

No, non è possibile eseguire operazioni sui certificati usando un modello di Azure Resource Manager (ARM). Una soluzione alternativa consigliata consiste nell'usare i metodi di importazione dei certificati nell'API di Azure, nell'interfaccia della riga di comando di Azure o in PowerShell. Se si dispone di un certificato esistente, è possibile importarlo come segreto.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Quando si importa un certificato tramite la portale di Azure, si verifica un errore "si è verificato un problema". Come posso esaminare ulteriormente?
    
Per visualizzare un errore più descrittivo, importare il file del certificato usando [l'interfaccia della](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) riga di comando di Azure o [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Come è possibile risolvere "tipo di errore: accesso negato o l'utente non è autorizzato a importare il certificato"?
    
Per l'operazione di importazione è necessario concedere all'utente le autorizzazioni per importare il certificato nei criteri di accesso. A tale scopo, passare all'insieme di credenziali delle chiavi, selezionare **criteri di accesso**  >  **Aggiungi criteri di accesso**  >  **selezionare** l'  >  **entità** autorizzazioni per il certificato, cercare l'utente e quindi aggiungere l'indirizzo di posta elettronica dell'utente. 

Per ulteriori informazioni sui criteri di accesso relativi ai certificati, vedere [about Azure Key Vault Certificates](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Come è possibile risolvere il problema relativo al tipo di errore: conflitto durante la creazione di un certificato?
    
Ogni nome di certificato deve essere univoco. Un certificato con lo stesso nome potrebbe essere in uno stato di eliminazione temporanea. Inoltre, in base alla [composizione di un certificato](./about-certificates.md#composition-of-a-certificate), quando viene creato un nuovo certificato, viene creato un segreto indirizzabile con lo stesso nome. Se nell'insieme di credenziali delle chiavi è presente un'altra chiave o segreto con lo stesso nome di quello che si sta provando a specificare per il certificato, la creazione del certificato avrà esito negativo e sarà necessario rimuovere la chiave o il segreto o usare un nome diverso 

Per ulteriori informazioni, vedere [get deleted certificate Operation](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Perché ricevo un messaggio di errore di tipo: la lunghezza del carattere è troppo lunga?
Questo errore può essere causato da uno dei due motivi seguenti:    
* Il nome del soggetto del certificato è limitato a 200 caratteri.
* La password del certificato è limitata a 200 caratteri.

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>È possibile importare un certificato scaduto per Azure Key Vault?
    
No, i certificati PFX scaduti non possono essere importati in Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Come è possibile convertire il certificato nel formato appropriato?

È possibile richiedere all'autorità di certificazione di fornire il certificato nel formato richiesto. Sono disponibili anche strumenti di terze parti che consentono di convertire il certificato nel formato corretto.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>È possibile importare I certificati da autorità di certificazione non partner?
Sì, è possibile importare i certificati da qualsiasi CA, ma l'insieme di credenziali delle chiavi non potrà rinnovarli automaticamente. È possibile impostare i promemoria per ricevere notifiche sulla scadenza del certificato.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Se si importa un certificato da una CA partner, la funzionalità di rinnovo automatico continuerà a funzionare?
Sì. Dopo aver caricato il certificato, assicurarsi di specificare la rotazione automatica nei criteri di rilascio del certificato. Le impostazioni rimarranno attive fino al rilascio del ciclo successivo o della versione del certificato.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Perché non è possibile visualizzare il certificato del servizio app importato per Key Vault? 
Se il certificato è stato importato correttamente, sarà possibile confermarlo selezionando il riquadro **Secrets (segreti** ).


## <a name="next-steps"></a>Passaggi successivi

- [Certificati Azure Key Vault](./about-certificates.md)