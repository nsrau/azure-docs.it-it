<properties 
   pageTitle="Microsoft Azure AD Connect - Eseguire l'aggiornamento dallo strumento Microsoft Azure AD Sync (DirSync)" 
   description="Informazioni su come eseguire l'aggiornamento da DirSync ad Azure AD Connect. Questo articolo descrive i passaggi necessari per aggiornare lo strumento Microsoft Azure AD Sync (DirSync) ad Azure AD Connect." 
   services="active-directory" 
   documentationCenter="" 
   authors="shoatman" 
   manager="terrylanfear" 
   editor="billmath"/>

<tags 
   ms.service="active-directory" 
   ms.workload="identity" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="05/26/2015" 
   ms.author="shoatman"/>

# Aggiornamento dello strumento di sincronizzazione di Microsoft Azure Active Directory (DirSync) ad Azure Active Directory Connect

La documentazione seguente semplifica l'aggiornamento dell'installazione di DirSync esistente ad Azure AD Connect

## Scaricare Azure AD Connect

Per iniziare a usare Azure AD Connect, è possibile scaricare la versione più recente dalla pagina del [download di Azure AD Connect in versione di anteprima pubblica](http://connect.microsoft.com/site1164/program8612).

## Prima di installare Azure AD Connect
Prima di installare Azure AD Connect e di eseguire l'aggiornamento da DirSync, sono necessari alcuni elementi.

- Password dell'account amministratore globale per l'istanza di Azure AD (l'account verrà indicato durante l'installazione)
- Un account amministratore dell'organizzazione per Active Directory locale
- Facoltativo: se DirSync è stato configurato per l'uso di una versione completa di SQL Server, informazioni per questa istanza di database.

### Distribuzione parallela

Se è necessario sincronizzare più di 50.000 oggetti, è possibile scegliere di eseguire una distribuzione parallela. Per la distribuzione parallela è necessario un server o un set di server separato (se è necessario un server separato per SQL Server). Il vantaggio di una distribuzione parallela consiste nell'opportunità di evitare i tempi di interruzione della sincronizzazione. L'installazione di Azure AD Connect tenterà di stimare i tempi di inattività previsti, ma se in passato è stato aggiornato DirSync, l'esperienza personale costituirà l'indicazione migliore.

## Installazione di Azure AD Connect

Scaricare Azure AD Connect e copiarlo nel server DirSync esistente.

1. Selezionare e fare doppio clic su AzureADConnect.msi.
2. Avviare i passaggi della procedura guidata

Per un aggiornamento sul posto, si verificano i passaggi generali seguenti:

1. Avvio di Azure AD Connect
2. Analisi della configurazione di DirSync corrente
3. Raccolta della password amministratore globale di Azure AD
4. Raccolta delle credenziali per un account amministratore dell'organizzazione (usato solo durante l'installazione di Azure AD Connect)
5. Installazione di AAD Connect
    * Disinstallazione di DirSync
	* Installare AAD Connect
	* Avvio della sincronizzazione (facoltativo)

Nei casi seguenti sono necessari altri passaggi e informazioni:

* Si usa attualmente una versione completa di SQL Server, locale o remota
* Nell'ambito si trovano più di 50.000 oggetti per la sincronizzazione

## Aggiornamento sul posto, meno di 50.000 oggetti, SQL Express (procedura dettagliata)

0. Avviare il programma di installazione di Azure AD Connect (MSI)

1. Leggere e accettare le condizioni di licenza e l'informativa sulla privacy.

![Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)

2. Fare clic su Avanti per avviare l'analisi dell'installazione di DirSync esistente

![Analisi dell'installazione della sincronizzazione di directory esistente](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)

3. Al termine dell'analisi, vengono visualizzati alcuni consigli su come procedere. In questo scenario (meno di 50.000 oggetti con SQL Express) viene visualizzata la schermata seguente.

![Analisi completata. È possibile eseguire l'aggiornamento da DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)

4. Fornire la password per l'account attualmente usato per la connessione ad Azure AD.

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)

5. Fornire un account amministratore dell'organizzazione per Active Directory.

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)

6. È ora possibile procedere alla configurazione. Quando si fa clic su Avanti, viene disinstallato DirSync e viene configurato Azure AD Connect, quindi viene avviata la sincronizzazione.  

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## Aggiornamento sul posto, più di 50.000 oggetti
Al passaggio 3 viene visualizzato un messaggio diverso se nell'ambito si trovano più di 50.000 oggetti per la sincronizzazione. Verrà visualizzata una schermata simile alla seguente:

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

In questo caso, è consigliabile valutare se eseguire un aggiornamento parallelo su un server separato. Il motivo di questo consiglio è che a seconda delle dimensioni dell'organizzazione, un aggiornamento sul posto può avere impatto sui contratti di servizio con l'azienda per quanto riguarda la velocità con cui le modifiche apportate ad Active Directory locale vengono applicate ad Azure AD/Office 365. Viene tentata una stima del tempo necessario per la prima sincronizzazione con Azure AD Connect. Come citato sopra, l'esperienza personale riguardo all'installazione originale di DirSync o agli aggiornamenti di DirSync costituirà probabilmente l'indicatore migliore.

Per la distribuzione parallela sono necessari uno o più server separati (se SQL Server deve essere eseguito su un server diverso da Azure AD Connect). Per questo motivo, è assolutamente sensato considerare un aggiornamento sul posto se può essere pianificato in modo da evitare conseguenze negative all'interno dell'organizzazione.

Per continuare con un aggiornamento sul posto, fare clic sulla casella di controllo accanto al messaggio che indica di continuare ad aggiornare DirSync nel computer.

## Aggiornamento sul posto, versione completa di SQL Server

Al passaggio 3 viene visualizzato un messaggio diverso se l'installazione di DirSync usa una versione completa locale o remota di SQL Server. Verrà visualizzata una schermata simile alla seguente:

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)

Vengono visualizzate le informazioni relative al server di database di SQL Server esistente usato da DirSync. Se necessario, apportare le modifiche appropriate. Facendo clic su "Avanti", l'installazione continuerà.

## Distribuzione parallela, più di 50.000 oggetti

Se sono presenti più di 50.000 oggetti, al passaggio 3 l'installazione di Azure AD Connect consiglia una distribuzione parallela. Vedere "Aggiornamento sul posto, più di 50.000 oggetti" sopra per informazioni su quale opzione scegliere tra la distribuzione di Azure AD Connect sul posto o parallela. Verrà visualizzata una schermata simile alla seguente:

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Se si vuole procedere con la distribuzione parallela, è necessario eseguire le operazioni seguenti:

- Fare clic sul pulsante "Esporta impostazioni". Quando si installa Azure AD Connect in un server separato, queste impostazioni vengono importate per eseguire la migrazione di tutte le impostazioni dall'installazione di DirSync corrente alla nuova installazione di AAD Connect.

Una volta esportate le impostazioni, è possibile chiudere la procedura guidata di Azure AD Connect nel server DirSync.

### Installazione di Azure AD Connect in un server separato

Quando si installa Azure AD Connect in un nuovo server, il programma di installazione non trova DirSync e presuppone di dover eseguire un'installazione pulita di Azure AD Connect. Ecco un paio di passaggi speciali:

1. Eseguire il programma di installazione di Azure AD Connect (MSI).
2. Quando viene visualizzata la schermata iniziale di Azure AD Connect, chiudere la procedura guidata facendo clic sulla "X" nell'angolo in alto a destra della finestra.
3. Aprire un prompt dei comandi.
4. Dall'installazione locale di Azure AD Connect (percorso predefinito: C:\Programmi\Microsoft Azure Active Directory Connect) eseguire il comando seguente:
    * AzureADConnect.exe /migrate

Azure AD Connect viene avviato e visualizza l'interfaccia utente seguente:

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)

5. Selezionare il file di impostazioni esportato dall'installazione di DirSync.
6. Configurare tutte le opzioni avanzate, tra cui:
    * Un percorso di installazione personalizzato per Azure AD Connect
	* Un'istanza esistente di SQL Server (per impostazione predefinita, Azure AD Connect installa SQL Server 2012 Express)
	* Un account di servizio usato per connettersi a SQL Server (se il database di SQL Server è remoto, deve essere un account di servizio del dominio)

Vedere queste opzioni nell'interfaccia utente seguente:

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)

7. Fare clic su Avanti. 
8. Nella pagina "Pronto per la configurazione" lasciare deselezionata l'opzione "Avviare il processo di sincronizzazione non appena viene completata la configurazione iniziale".[AZURE.NOTE]Verrà avviata la sincronizzazione tra Windows Server Active Directory e Azure Active Directory, ma non verranno esportate modifiche in Azure AD. Le modifiche possono essere esportare attivamente da un solo strumento di sincronizzazione alla volta.
9. Fare clic su "Installa".

[AZURE.NOTE]È necessario deselezionare la casella di controllo per fare in modo che DirSync, che è ancora installato e in esecuzione, e Azure AD Connect non tentino di scrivere in Azure AD contemporaneamente.

### Verificare che Azure AD Connect sia pronto per avviare la sincronizzazione

Per determinare se Azure AD Connect sia pronto o meno a continuare al posto di DirSync, è necessario aprire il gestore del servizio di sincronizzazione di Azure AD Connect. Una ricerca di "Sincronizzazione" dal menu Start di Windows permetterà di trovare questa applicazione.

All'interno dell'applicazione sarà necessario visualizzare la scheda "Operazioni". In questa scheda verificare che le operazioni seguenti siano state completate:

- Importazione nell'agente di gestione di Active Directory
- Importazione nell'agente di gestione di Azure AD
- Sincronizzazione completa nell'agente di gestione di Active Directory
- Sincronizzazione completa nell'agente di gestione di Azure AD

Al termine di queste quattro operazioni, è possibile disinstallare DirSync e consentire la sincronizzazione di Azure AD Connect.

### Disinstallare DirSync (vecchio server)

- In "Installazione applicazioni" individuare "Strumento di sincronizzazione di Microsoft Azure Active Directory"
- Disinstallare "Strumento di sincronizzazione di Microsoft Azure Active Directory"

### Aprire Azure AD Connect (nuovo server)
Al termine dell'installazione, la riapertura di Azure AD Connect permette di accedere a un'esperienza di configurazione. Aprire Azure AD Connect.

Dovrebbe essere visualizzata la seguente schermata:

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* Selezionare "Configurazione della modalità di gestione temporanea"
    * L'aggiornamento da DirSync usando impostazioni esportate attiva automaticamente la modalità di gestione temporanea per Azure AD Connect. La modalità di gestione temporanea comporta essenzialmente l'esecuzione della sincronizzazione all'interno di Azure AD Connect, ma senza l'esportazione delle modifiche in Azure AD o Active Directory.
* Per disattivare la gestione temporanea, deselezionare la casella di controllo "Abilitazione modalità di gestione temporanea".

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* Fare clic sul pulsante di installazione

A questo punto, viene completata la migrazione ad Azure AD Connect mediante una distribuzione parallela.

## Componenti di supporto di Azure AD Connect

Di seguito è riportato un elenco di prerequisiti e componenti di supporto che verranno installati da Azure AD Connect nel server su cui è stato impostato Azure AD Connect. L'elenco riguarda un'istallazione di SQL Express di base. Se si sceglie di usare una versione diversa di SQL Server nella pagina Installazione dei servizi di sincronizzazione, i componenti di SQL Server 2012 elencati di seguito non verranno installati.

- Connettore di Azure Active Directory per Forefront Identity Manager
- Utilità della riga di comando di Microsoft SQL Server 2012
- Client nativo di Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Modulo di Azure Active Directory per Windows PowerShell
- Assistente per l'accesso a Microsoft Online Services per professionisti IT
- Microsoft Visual C++ 2013 Redistribution Package


**Risorse aggiuntive**

* [Usare l'infrastruttura di identità locale nel cloud](active-directory-aadconnect.md)
* [Funzionamento di Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Novità di Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Altre informazioni](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=62-->