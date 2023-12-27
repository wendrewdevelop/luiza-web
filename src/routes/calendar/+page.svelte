<script>
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';

    
    let schedulers = [];
    let notifications = [];
    let storedData;
    let showModal = false;
    let currentDate = new Date();
    let currentMonthIndex = currentDate.getMonth();
    let months = [
        'Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho',
        'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'
    ];
    let currentMonthName = months[currentMonthIndex];
    let currentYear = currentDate.getFullYear();
    let currentMonth = currentDate.getMonth() + 1; 
    let daysInMonth = new Date(currentYear, currentMonth, 0).getDate();
    let allDays = Array.from({ length: daysInMonth }, (_, index) => index + 1);

    // Function to get the day of the week (0-indexed) for a given date
    function getDayOfWeek(day) {
        const dayOfWeek = new Date(currentYear, currentMonth - 1, day).getDay();
        return dayOfWeek === 0 ? 6 : dayOfWeek - 1; // Adjusting Sunday (0) to 6
    }

    function openModal() {
        showModal = true;
    }
    function closeModal() {
        showModal = false;
    }

    onMount(() => {
        // Check if the value exists in sessionStorage
        if (sessionStorage.getItem('token')) {
            // Retrieve the value from sessionStorage
            storedData = true;
        } else {
            // Set a default value if it doesn't exist
            storedData = false;
            goto('/login');
        }
    });

    onMount(async () => {
        await fetchData();
    });

    async function fetchData() {
        try {
        const response = await fetch('http://127.0.0.1:8000/api/v1/scheduler/');
        const data = await response.json();
        schedulers = data.results;
        console.log(schedulers)
        } catch (error) {
            console.error('Error fetching data:', error);
        }
    }

    function destroySessionItem() {
        // Remove the item from sessionStorage
        sessionStorage.removeItem('token');
        // Update the component state
        storedData = false; // or set it to a default value
        goto('/login');
    }
</script>


<div style="display: contents">
    <div class="wrapper">
        <div class="sidebar" data-color="purple" data-background-color="white" data-image="../assets/img/sidebar-1.jpg">
            <!--
                Tip 1: You can change the color of the sidebar using: data-color="purple | azure | green | orange | danger"
        
                Tip 2: you can also add an image using data-image tag
            -->
            <div class="logo">
                <a href="/" class="simple-text logo-normal">
                    <h3>Luiza</h3>
                    <sub>Software escolar</sub>
                </a>
            </div>
            <div class="sidebar-wrapper">
                <ul class="nav">
                    <li class="nav-item active ">
                        <a class="nav-link" href="#">
                            <i class="material-icons">dashboard</i>
                            <p>Página inicial</p>
                        </a>
                    </li>
                    <li class="nav-item ">
                        <a class="nav-link" href="#">
                            <i class="material-icons">person</i>
                            <p>Cadastros</p>
                        </a>
                    </li>
                    <li class="nav-item ">
                        <a class="nav-link" href="/calendar">
                            <i class="material-icons">calendar_today</i>
                            <p>Agenda</p>
                        </a>
                    </li>
                    <li class="nav-item ">
                        <a class="nav-link" href="#">
                            <i class="material-icons">library_books</i>
                            <p>Typography</p>
                        </a>
                    </li>
                    <li class="nav-item ">
                        <a class="nav-link" href="#">
                            <i class="material-icons">bubble_chart</i>
                            <p>Icons</p>
                        </a>
                    </li>
                    <li class="nav-item ">
                        <a class="nav-link" href="#">
                            <i class="material-icons">location_ons</i>
                            <p>Maps</p>
                        </a>
                    </li>
                    <li class="nav-item ">
                        <a class="nav-link" href="#">
                            <i class="material-icons">notifications</i>
                            <p>Notifications</p>
                        </a>
                    </li>
                    <hr>
                    {#if storedData}
                    <li class="nav-item ">
                        <a class="nav-link text-danger" href="#" on:click={destroySessionItem}>
                            <i class="material-icons text-danger">logout</i>
                            <p>Sair</p>
                        </a>
                    </li>
                    {:else}
                    <li class="nav-item "></li>
                    {/if}
                </ul>
            </div>
        </div>
        <div class="main-panel">
            <!-- Navbar -->
            <nav class="navbar navbar-expand-lg navbar-transparent  navbar-absolute fixed-top">
                <div class="container-fluid">
                    <div class="navbar-wrapper">
                        <h1 class="navbar-brand">Página inicial</h1>
                    </div>
                    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navigation" aria-controls="navigation-index" aria-expanded="false" aria-label="Toggle navigation">
                        <span class="sr-only">Toggle navigation</span>
                        <span class="navbar-toggler-icon icon-bar"></span>
                        <span class="navbar-toggler-icon icon-bar"></span>
                        <span class="navbar-toggler-icon icon-bar"></span>
                    </button>
                </div>
            </nav>
            <!-- End Navbar -->
            <div class="content">
                <div class="container-fluid">
                    <div class="row">
                        <div class="col-lg-12 col-md-12">
                            <div class="card">
                                <div class="card-header card-header-tabs card-header-primary">
                                    <div class="nav-tabs-navigation">
                                        <div class="nav-tabs-wrapper">
                                            <ul class="nav nav-tabs" data-tabs="tabs">
                                                <li class="nav-item">
                                                    <a class="nav-link active" href="#profile" data-toggle="tab">
                                                        <i class="material-icons">calendar_today</i> Calendário escolar
                                                        <div class="ripple-container"></div>
                                                    </a>
                                                </li>
                                            </ul>
                                        </div>
                                    </div>
                                </div>
                                <div class="card-body d-flex justify-content-center">
                                    <div class="calendar text-center">
                                        <div class="month"><h>{currentMonthName}</h></div>
                                        <table id="table-calendar">
                                            <tr>
                                                <th>Segunda</th>
                                                <th>Terça</th>
                                                <th>Quarta</th>
                                                <th>Quinta</th>
                                                <th>Sexta</th>
                                                <th>Sabado</th>
                                                <th>Domingo</th>
                                            </tr>
                                            {#each allDays as day (day)}
                                                {#if getDayOfWeek(day) === 0}
                                                    {@html `<tr>`}
                                                {/if}
                                                <td tabindex={day}>
                                                    {day}<br>
                                                    {#each schedulers.filter(scheduler => scheduler.day === day) as scheduler, id}
                                                        <a href="#" style="background-color: #034efc; 
                                                            border-radius: 5px; 
                                                            margin-bottom: 1px;
                                                            color: white;" on:click={() => openModal(day)}>{scheduler.title}</a>

                                                            {#if showModal}
                                                                <div class="modal">
                                                                    <div class="modal-content" id="{id}">
                                                                        <span class="close" on:click={closeModal}>&times;</span>
                                                                        <!-- Modal content goes here -->
                                                                        <h2>This is a modal</h2>
                                                                        <p>Modal content goes here...</p>
                                                                    </div>
                                                                </div>
                                                            {/if}
                                                    {/each}
                                                </td>
                                                {#if getDayOfWeek(day) === 6 || day === daysInMonth}
                                                    {@html `</tr>`}
                                                {/if}
                                            {/each}

                                        </table>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<style>
    :root {
        --bg-color:#4fe3c1;
        --calendar-bg: #ffffff;
        --num-color: #6d7173;
        --month-color: #34393c;
        --day-color: #9ba8b7;
        --td-bg-hover-color: #edf2f5;
    }
    * {
        box-sizing: border-box;
        padding: 0;
        margin: 0;
    }
    body {
        height: 100vh;
        width: 100vw;
        background-color: var(--bg-color);
        display: flex;
        justify-content: center;
        align-items: center;
        user-select: none;
        -moz-user-select: none;
        -webkit-user-select: none;
        font-family: 'Poppins', sans-serif;
    }
    h {
        font-size: 1em;
        font-weight: 600;
        color: var(--month-color);
        text-transform: capitalize;
    }
    .calendar {
        height: 75vh;
        width: 50vw;
        background-color: var(--calendar-bg);
    }
    .month {
        width: 100%;
        height: 70px;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    table {
        width: 100%;
    }
    th {
        text-transform: uppercase;
        font-size: 0.8em;
        font-weight: 400;
        color: var(--day-color);
    }
    td {
        text-align: center;
        color: var(--num-color);
        font-size: 15px;
        padding: 20px;
        transition: all .2s ease;
    }
    td:hover {
        background-color: var(--td-bg-hover-color);
    }
    td:focus {
        background-color: var(--bg-color);
        color: var(--calendar-bg);
        outline: none;
        border: none;
        border-radius: 3px;
    }
    @media screen and (max-width: 900px) {
        .calendar {
            width: 100vw;
            height: auto;
        }
    }
</style>