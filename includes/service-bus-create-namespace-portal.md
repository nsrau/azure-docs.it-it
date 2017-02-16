## <a name="create-a-service-namespace"></a>Creare uno spazio dei nomi del servizio

Per iniziare a usare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi. Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione. 

Per creare uno spazio dei nomi:

1. Accedere al [portale di Azure][Azure portal].
2. Nel riquadro di spostamento sinistro del portale fare clic su **Nuovo**, quindi su **Enterprise Integration** e infine su **Bus di servizio**.
3. Nella finestra di dialogo **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.
4. Dopo aver verificato che il nome dello spazio dei nomi sia disponibile, scegliere il piano tariffario, ovvero Basic, Standard o Premium.
5. Nel campo **Sottoscrizione** scegliere una sottoscrizione di Azure in cui creare lo spazio dei nomi.
6. Nel campo **Gruppo di risorse** scegliere un gruppo di risorse esistente nel quale risiederà lo spazio dei nomi oppure crearne uno nuovo.      
7. In **Località**scegliere il paese o l'area in cui deve essere ospitato lo spazio dei nomi.
   
    ![Crea spazio dei nomi][create-namespace]
8. Fare clic su **Crea**. A questo punto, lo spazio dei nomi verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.

### <a name="obtain-the-management-credentials"></a>Ottenere le credenziali di gestione
1. Nell'elenco degli spazi dei nomi fare clic sul nome dello spazio dei nomi appena creato.
2. Nel pannello dello spazio dei nomi fare clic su **Criteri di accesso condivisi**.
3. Nel pannello **Criteri di accesso condivisi** fare clic su **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Nel pannello **Criteri: RootManageSharedAccessKey** fare clic sul pulsante Copia accanto a **Stringa di connessione - chiave primaria** per copiare la stringa di connessione negli Appunti e usarla in un secondo momento. Incollare questo valore nel Blocco note o in un'altra posizione temporanea.
   
    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure portal]: https://portal.azure.com

<!--HONumber=Jan17_HO3-->


