
Per impostazione predefinita, le API in un back-end dell'app per dispositivi mobili possono essere richiamate in modo anonimo. È necessario limitare l'accesso solo ai client autenticati.

1. Nel computer aprire il progetto server in Visual Studio e passare a **Controller** > **TodoItemController.cs**.

2. Aggiungere l'attributo `[Authorize]` alla classe **TodoItemController**, come indicato di seguito: È necessario che tutte le operazioni sulla tabella TodoItem vengano eseguite da un utente autenticato. Per limitare l'accesso solo a metodi specifici, è inoltre possibile applicare questo attributo solo a tali metodi anziché alla classe.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    È necessario che tutte le operazioni sulla tabella TodoItem vengano eseguite da un utente autenticato. Per limitare l'accesso solo a metodi specifici, è inoltre possibile applicare questo attributo solo a tali metodi anziché alla classe.
   
3. Pubblicare di nuovo il progetto server.

<!---HONumber=August15_HO8-->