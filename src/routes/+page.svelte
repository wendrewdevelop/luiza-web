<script>
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';

    
    let students = [];
    let notifications = [];
    let storedData;

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
        await fetchDataNotifications();
    });

    async function fetchData() {
        try {
        const response = await fetch('http://127.0.0.1:8000/api/v1/student/');
        const data = await response.json();
        students = data.results;
        } catch (error) {
            console.error('Error fetching data:', error);
        }
    }
    async function fetchDataNotifications() {
        try {
        const response = await fetch('http://127.0.0.1:8000/api/v1/notification/');
        const data = await response.json();
        notifications = data.results;
        console.log(notifications);
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
                                                        <i class="material-icons">newspaper</i> Quadro de noticias
                                                        <div class="ripple-container"></div>
                                                    </a>
                                                </li>
                                            </ul>
                                        </div>
                                    </div>
                                </div>
                                <div class="card-body">
                                    <div class="row">
                                        {#each notifications as notification}
                                            <div class="col-lg-4 col-md-4">
                                                <div class="card">
                                                    <div class="card-header card-header-tabs card-header-primary">
                                                        {notification.title}
                                                    </div>
                                                    <div class="card-body text-center">
                                                        {notification.message}
                                                    </div>
                                                </div>
                                            </div>
                                        {/each}
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
