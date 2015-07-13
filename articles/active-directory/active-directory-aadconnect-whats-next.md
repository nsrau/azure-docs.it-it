<properties 
	pageTitle="Gestione di Azure AD Connect" 
	description="Informazioni su come estendere la configurazione predefinita e attività operative per Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Gestione di Azure AD Connect 

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Che cos&apos;è">Che cos'è</a> <a href="../active-directory-aadconnect-how-it-works/" title="Come funziona">Come funziona</a> <a href="../active-directory-aadconnect-get-started/" title="Introduzione">Introduzione</a> <a href="../active-directory-aadconnect-whats-next/" title="Passaggi successivi" class="current">Passaggi successivi</a> <a href="../active-directory-aadconnect-learn-more/" title="Altre informazioni">Altre informazioni</a>
</div>

Di seguito sono riportati argomenti operativi avanzati che permettono di personalizzare Azure Active Directory Connect per soddisfare le esigenze e i requisiti dell'organizzazione.

## Assegnazione delle licenze agli utenti di Azure AD Premium ed Enterprise Mobility

Dopo avere sincronizzato gli utenti nel cloud, occorre assegnare loro una licenza in modo che possano usare le app cloud come Office 365.

### Per assegnare una licenza Azure AD Premium o Enterprise Mobility Suite
--------------------------------------------------------------------------------
<ol>
<li>Accesso al portale di Azure come amministratore.</li>
<li>A sinistra selezionare **Active Directory**.</li>
<li>Nella pagina Active Directory fare doppio clic sulla directory con gli utenti da abilitare.</li>
<li>Nella parte superiore della pagina della directory selezionare **Licenze**.</li>
<li>Nella pagina Licenze selezionare Active Directory Premium o Enterprise Mobility Suite, quindi fare clic su **Assegna**.</li>
<li>Nella finestra di dialogo selezionare gli utenti a cui assegnare le licenze, quindi fare clic sull'icona con il segno di spunta per salvare le modifiche.</li>


## Verifica dell'attività di sincronizzazione pianificata
Se si desidera controllare lo stato di una sincronizzazione, verificare nel portale di Azure.

### Per verificare l'attività di sincronizzazione pianificata
--------------------------------------------------------------------------------

1. Accesso al portale di Azure come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina Active Directory fare doppio clic sulla directory con gli utenti da abilitare.
4. Nella parte superiore della pagina della directory selezionare **Integrazione directory**.
5. Nella sezione relativa all'integrazione con Active Directory locale prendere nota dell'ora dell'ultima sincronizzazione.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## Avvio di un'attività di sincronizzazione pianificata
Se è necessario eseguire un'attività di sincronizzazione, è possibile eseguire nuovamente la procedura guidata di Azure AD Connect. È necessario fornire le credenziali di Azure AD. Nella procedura guidata selezionare l'attività **Personalizzazione delle opzioni di sincronizzazione** e fare clic su Avanti nella procedura guidata. Al termine, verificare che la casella **Avviare il processo di sincronizzazione non appena viene completata la configurazione iniziale** sia selezionata.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>




## Attività aggiuntive disponibili in Azure AD Connect
Dopo l'installazione iniziale di Azure AD Connect è sempre possibile riavviare la procedura guidata dalla pagina iniziale di Azure AD Connect o dal collegamento sul dekstop. Si noterà che la riesecuzione della procedura guidata fornisce alcune nuove opzioni sotto forma di Attività aggiuntive.

La tabella seguente include un riepilogo di tali attività e una breve descrizione di ognuna di esse.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next/addtasks.png) </center>

Attività aggiuntive | Descrizione 
------------- | ------------- |
Visualizzare lo scenario selezionato |Consente di visualizzare la soluzione di Connect di Azure AD corrente. Ciò include le impostazioni generali, le directory sincronizzate, le impostazioni di sincronizzazione e così via.
Personalizzare le opzioni di sincronizzazione | Consente di modificare la configurazione corrente, inclusa l'aggiunta di altre foreste di Active Directory per la configurazione o l'attivazione di opzioni di sincronizzazione, ad esempio writeback di utenti, gruppi, dispositivi o password.
Abilitazione modalità di gestione temporanea | Consente di inserire in un'area di gestione temporanea le informazioni che saranno sincronizzate in un secondo momento, senza che avvenga l'esportazione in Azure AD o Active Directory. Consente di visualizzare in anteprima le sincronizzazioni prima che si verifichino.


 






## Documentazione aggiuntiva
Per altra documentazione sull'uso di Azure AD Connect vedere gli articoli seguenti:

- [Modifica della configurazione predefinita di Azure AD Connect](active-directory-aadconnect-whats-next-change-default-config.md)
- [Uso dell'Editor regole di sincronizzazione di Azure AD Connect](active-directory-aadconnect-whats-next-synch-rules-editor.md)
- [Uso del provisioning dichiarativo](active-directory-aadconnect-whats-next-declarative-prov.md)

Parte della documentazione creata per Azure AD Sync è comunque pertinente e si applica anche ad Azure AD Connect. Benché grande sia l'impegno per aggiungere questa documentazione in Azure.com, una sua parte si trova ancora nella sezione specifica di MSDN Library. Per altra documentazione, vedere [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) e [Azure AD Sync in MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).


 

<!---HONumber=62-->