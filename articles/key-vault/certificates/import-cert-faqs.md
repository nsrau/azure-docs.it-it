---
title: Domande frequenti-Azure Key Vault importazione di certificati
description: Domande frequenti-Azure Key Vault importazione di certificati
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098268"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Domande frequenti-Azure Key Vault importazione di certificati

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Come è possibile importare un certificato in Azure Key Vault?

Importa certificato: per l'operazione di importazione, Azure Key Vault accetta due formati di certificato PEM e PFX. Anche se sono presenti file PEM con solo la parte pubblica, l'insieme di credenziali delle chiavi di Azure richiede e accetta solo un PEM o PFX nella cartella di file e con una chiave privata. Seguire l' [esercitazione per importare il certificato](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Dopo aver importato il certificato protetto da password nell'insieme di credenziali delle chiavi e averlo scaricato, non è possibile visualizzare la password associata al certificato?
    
Il certificato protetto caricato dopo l'archiviazione nell'insieme di credenziali delle chiavi non salva la password associata. Questa operazione è necessaria solo una volta durante l'importazione. Sebbene si tratta di un concetto di progettazione, è sempre possibile ottenere il certificato come segreto ed eseguire la conversione da Base64 a PFX aggiungendo la password precedente tramite [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Come è possibile risolvere un errore di parametro non valido? Quali sono i formati di certificato supportati per l'importazione in Key Vault?

Quando si importa il certificato, è necessario assicurarsi che la chiave sia inclusa nel file stesso. Se la chiave privata è separata in un formato diverso, è necessario combinare la chiave con il certificato. Alcune autorità di certificazione forniscono certificati in formati diversi, pertanto prima di importare il certificato, verificare che siano in formato. pem o. pfx e che la chiave utilizzata sia RSA o ECC. Per esaminare i requisiti dei [certificati](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) e i [requisiti chiave del certificato](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection), fare riferimento a questi.

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Errore durante l'importazione del certificato tramite il portale "Si è verificato un problema". Come posso esaminare ulteriormente?
    
Per visualizzare un errore più descrittivo, importare il file del certificato tramite l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Come è possibile risolvere il tipo di errore: accesso negato o l'utente non è autorizzato a importare il certificato?
    
Questa operazione richiede l'autorizzazione di importazione certificati. Passare alla posizione in cui si trova Key Vault, sarà necessario concedere all'utente le autorizzazioni appropriate nei criteri di accesso. Passare al Key Vault criteri di accesso> > aggiungere i criteri di accesso > selezionare le autorizzazioni per il certificato (o come si desiderano le autorizzazioni) > entità > cercare e quindi aggiungere la posta elettronica dell'utente. [Altre informazioni sui criteri di accesso correlati ai certificati](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Come è possibile risolvere il tipo di errore: conflitto durante la creazione di un certificato?
    
Il nome del certificato deve essere univoco. Il certificato con lo stesso nome potrebbe essere in stato di eliminazione temporanea, anche in base alla [composizione di un certificato](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) nell'insieme di credenziali delle chiavi di Azure, se è presente un'altra chiave o segreto nel Key Vault con lo stesso nome che si sta provando a specificare per il certificato, l'operazione avrà esito negativo e sarà necessario rimuovere la chiave o il segreto o usare un nome diverso per il certificato. [Visualizza certificato eliminato](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Perché viene trovato il tipo di errore: la lunghezza del carattere è troppo lunga?
    
* La lunghezza del nome del soggetto del certificato ha un limite di caratteri di 200 caratteri
* La lunghezza della password del certificato ha un limite di caratteri di 200 caratteri

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>È possibile importare un certificato scaduto in Azure Key Vault?
    
No, i certificati PFX scaduti non vengono importati in Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Come è possibile convertire il certificato nel formato appropriato?

È possibile richiedere all'autorità di certificazione di fornire il certificato nel formato necessario, inoltre sono disponibili strumenti di terze parti che consentono di eseguire la conversione nel formato appropriato. Tuttavia, Microsoft non sarà in grado di consigliare ulteriori informazioni su come ottenere il certificato nel formato desiderato.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>È possibile importare I certificati da autorità di certificazione non partner?
Sì, è possibile importare i certificati da qualsiasi CA, ma Key Vault non sarà in grado di rinnovare automaticamente tali certificati. Sarà possibile impostare le notifiche tramite posta elettronica per ricevere una notifica sulla scadenza del certificato.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Se si importa un certificato da una CA partner, la funzionalità di rinnovo automatico continuerà a funzionare?
Sì, è necessario assicurarsi che dopo il caricamento venga specificata la rotazione automatica nei criteri di rilascio del certificato. Inoltre, le modifiche verranno riflesse fino alla versione successiva del ciclo o del certificato.


## <a name="next-steps"></a>Passaggi successivi

- [Certificati Azure Key Vault](/azure/key-vault/certificates/about-certificates)
