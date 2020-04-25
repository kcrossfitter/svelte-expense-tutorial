<script>
  // import Github from "./Github.svelte";
  import GithubAwait from "./GithubAwait.svelte";
  import { setContext, onMount, afterUpdate } from "svelte";

  // components
  import Navbar from "./Navbar.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Totals from "./Totals.svelte";
  import ExpenseForm from "./ExpenseForm.svelte";
  import Modal from "./Modal.svelte";

  // ------ data
  // import expensesData from "./expenses";

  // ------ variables
  // let expenses = [...expensesData];
  let expenses = [];
  // for editing purpose
  let setName = "";
  let setAmount = null;
  let setId = null;
  // for form open and close
  let isFormOpen = false;

  // reactive
  $: isEditing = setId ? true : false;
  $: total = expenses.reduce((acc, current) => {
    return (acc += current.amount);
  }, 0);

  // functions
  function removeExpense(id) {
    expenses = expenses.filter(item => item.id !== id);
    // setLocalStorage();
  }

  // function deleteExpense(event) {
  //   const { id, name } = event.detail;
  //   console.log(name);
  //   removeExpense(id);
  // }

  function clearExpenses() {
    expenses = [];
    // setLocalStorage();
  }

  function addExpense({ name, amount }) {
    let expense = { id: Math.random() * Date.now(), name, amount };
    expenses = [expense, ...expenses];
    // setLocalStorage();
  }

  function setModifiedExpense(id) {
    let expense = expenses.find(item => item.id === id);

    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;

    showForm();
  }

  function editExpense({ name, amount }) {
    expenses = expenses.map(item => {
      return item.id === setId
        ? { ...item, name: name, amount: amount }
        : { ...item };
    });
    setId = null;
    setName = "";
    setAmount = null;

    // setLocalStorage();
  }

  function showForm() {
    isFormOpen = true;
  }

  function hideForm() {
    isFormOpen = false;
    setName = "";
    setAmount = null;
    setId = null;
  }

  // context
  setContext("remove", removeExpense);
  setContext("modify", setModifiedExpense);

  // localstorage
  function setLocalStorage() {
    localStorage.setItem("expenses", JSON.stringify(expenses));
  }

  onMount(() => {
    expenses = localStorage.getItem("expenses")
      ? JSON.parse(localStorage.getItem("expenses"))
      : [];
  });
  afterUpdate(() => {
    console.log("after update");
    setLocalStorage();
  });
</script>

<!-- CSS/Styling -->

<!-- HTML -->
<Navbar {showForm} />
<main class="content">
  <!-- <GithubAwait /> -->
  <!-- <Github /> -->
  {#if isFormOpen}
    <Modal>
      <ExpenseForm
        {addExpense}
        {editExpense}
        {isEditing}
        name={setName}
        amount={setAmount}
        {hideForm} />
    </Modal>
  {/if}
  <Totals title="total expenses" {total} />
  <ExpensesList {expenses} />
  <button
    type="button"
    class="btn btn-primary btn-block"
    on:click={clearExpenses}>
    clear expenses
  </button>
  <!-- <ExpensesList {expenses} on:delete={deleteExpense} /> -->
</main>
