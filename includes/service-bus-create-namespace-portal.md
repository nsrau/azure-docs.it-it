Per iniziare a usare le entità di messaggistica del bus di servizio in Azure, prima di tutto è necessario creare uno spazio dei nomi con un nome univoco in Azure. Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi:

1. Accedere al [portale di Azure][Azure portal].
2. Nel riquadro di spostamento sinistro del portale fare clic su **+ Crea una risorsa**, quindi su **Enterprise Integration** e infine su **Bus di servizio**.
3. Nella finestra di dialogo **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.
4. Dopo aver verificato che il nome dello spazio dei nomi sia disponibile, scegliere il piano tariffario, ovvero Basic, Standard o Premium.
5. Nel campo **Sottoscrizione** scegliere una sottoscrizione di Azure in cui creare lo spazio dei nomi.
6. Nel campo **Gruppo di risorse** scegliere un gruppo di risorse esistente nel quale risiederà lo spazio dei nomi oppure crearne uno nuovo.      
7. In **Località**scegliere il paese o l'area in cui deve essere ospitato lo spazio dei nomi.
   
    ![Crea spazio dei nomi][create-namespace]
8. Fare clic su **Crea**. A questo punto, lo spazio dei nomi verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.

### <a name="obtain-the-management-credentials"></a>Ottenere le credenziali di gestione
Con la creazione di un nuovo spazio dei nomi viene generata automaticamente una regola di firma di accesso condiviso iniziale con una coppia associata di chiavi primaria e secondaria, ognuna delle quali concede il controllo completo su tutti gli aspetti dello spazio dei nomi. Per informazioni su come creare altre regole con diritti più limitati per mittenti e ricevitori normali, vedere [Autenticazione e autorizzazione del bus di servizio](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md). Per copiare la regola iniziale seguire questa procedura: 

1.  Fare clic su **tutte le risorse**, quindi fare clic sul nome di spazio dei nomi appena creato.
2. Nella finestra dello spazio dei nomi fare clic su **Criteri di accesso condiviso**.
3. Nella schermata **Criteri di accesso condiviso** fare clic su **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Nella finestra **Criteri: RootManageSharedAccessKey** fare clic sul pulsante Copia accanto a **Stringa di connessione - chiave primaria** per copiare la stringa di connessione negli Appunti e usarla in un secondo momento. Incollare questo valore nel Blocco note o in un'altra posizione temporanea.
   
    ![connection-string][connection-string]

5. Ripetere il passaggio precedente e copiare e incollare il valore della **chiave primaria** in un percorso temporaneo per usarlo in seguito.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
