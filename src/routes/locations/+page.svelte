<script>
    import { goto } from "$app/navigation";
    import { jwt, role } from '../store.js';
    import { onMount } from 'svelte';
    let listeLocations = [];
    let idEnCours = "";
    let idOnEdit = "";
    let id = "";
    let filmName = "";
    let filmType = "";
    let filmProducerName = "";
    let endDate = "";
    let district = "";
    let sourceLocationId = "";
    let filmDirectorName = "";
    let address = "";
    let startDate = "";
    let year = "";
    let geolocation = {};
    onMount(handleLocations);
    async function handleLocations() {
        try {
            const response = await fetch('http://localhost:3000/locations', {
                method: 'GET',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${$jwt}`
                },
            });
            const data = await response.json();
            listeLocations=data;
            if (data) {
                console.log("User logged in successfully");
                // redirect the user to the dashboard or show a message
            } else {
                goto("./");
                console.log("Invalid username or password");
                // show an error message
            }
        } catch (error) {
            goto("./");
            console.error(error);
        }
    }
    function handleAdd() {
        idEnCours = "yolegang";
        idOnEdit = "";
        resetLocation();
    }
    function handleEdit(obj) {
        idOnEdit = obj._id;
        idEnCours = "";
        id = obj._id;
        filmName = obj.filmName;
        filmType = obj.filmType;
        filmProducerName = obj.filmProducerName;
        endDate = obj.endDate;
        district = obj.district;
        sourceLocationId = obj.sourceLocationId;
        filmDirectorName = obj.filmDirectorName;
        address = obj.address;
        startDate = obj.startDate;
        year = obj.year;
    }
    async function handleDelete(id) {
        try {
            const response = await fetch(`http://localhost:3000/locations/${id}`, {
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${$jwt}`
                },
            });
            const data = await response.json();
            if (data.success) {
                console.log("User logged in successfully");
                // redirect the user to the dashboard or show a message
            } else {
                console.log("Invalid username or password");
                // show an error message
            }
        } catch (error) {
            console.error(error);
        }
        handleLocations();
    }
    function handleSee(id) {
        idEnCours = id;
        idOnEdit = "";
    }
    async function handleModify() {
        try {
            const response = await fetch(`http://localhost:3000/locations/${id}`, {
                method: 'PATCH',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${$jwt}`
                },
                body: JSON.stringify({ filmType, filmProducerName, endDate, filmName, district, geolocation, sourceLocationId,
                    filmDirectorName, address, startDate, year })
            });
            const data = await response.json();
            if (data) {
                console.log("User logged in successfully");
                // redirect the user to the dashboard or show a message
            } else {
                console.log("Invalid username or password");
                // show an error message
            }
        } catch (error) {
            console.error(error);
        }
        idEnCours = "";
        idOnEdit = "";
        handleLocations();
    }
    function handleKeydown() {
    }
    function resetLocation() {
        id = "";
        filmName = "";
        filmType = "";
        filmProducerName = "";
        endDate = "";
        district = "";
        sourceLocationId = "";
        filmDirectorName = "";
        address = "";
        startDate = "";
        year = "";
    }
    async function handleValidate() {
        try {
            const response = await fetch(`http://localhost:3000/locations`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${$jwt}`
                },
                body: JSON.stringify({ filmType, filmProducerName, endDate, filmName, district, geolocation, sourceLocationId,
                    filmDirectorName, address, startDate, year })
            });
            const data = await response.json();
            if (data) {
                console.log("User logged in successfully");
                // redirect the user to the dashboard or show a message
            } else {
                console.log("Invalid username or password");
                // show an error message
            }
        } catch (error) {
            console.error(error);
        }
        handleLocations();
    }
    function handleLogout() {
        goto("./");
    }
</script>

<h1>Liste Locations</h1>
<div><button on:click={() => handleLogout()}>Logout</button></div>
<div class="listeLocations">
    <ul>
        {#each listeLocations as objet}
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <li key={objet._id}><span on:click={() => handleSee(objet._id)}>{objet.filmName}</span>
                {#if $role === "admin"}
                    <button on:click={() => handleEdit(objet)}>Edit</button>
                    <button on:click={() => handleDelete(objet._id)}>X</button>
                {/if}
                <br />
                {#if objet._id==idEnCours}
                    <span>id : {objet._id}<br /></span>
                    <span>filmType : {objet.filmType}<br /></span>
                    <span>filmProducerName : {objet.filmProducerName}<br /></span>
                    <span>endDate : {objet.endDate}<br /></span>
                    <span>district : {objet.district}<br /></span>
                    <span>sourceLocationId : {objet.sourceLocationId}<br /></span>
                    <span>filmDirectorName : {objet.filmDirectorName}<br /></span>
                    <span>address : {objet.address}<br /></span>
                    <span>startDate : {objet.startDate}<br /></span>
                    <span>year : {objet.year}<br /></span>
                {/if}

                {#if objet._id==idOnEdit}
                    <span>filmName : </span><input bind:value={filmName}><br />
                    <span>filmType : </span><input bind:value={filmType}><br />
                    <span>filmProducerName : </span><input bind:value={filmProducerName}><br />
                    <span>endDate : </span><input bind:value={endDate}><br />
                    <span>district : </span><input bind:value={district}><br />
                    <span>sourceLocationId : </span><input bind:value={sourceLocationId}><br />
                    <span>filmDirectorName : </span><input bind:value={filmDirectorName}><br />
                    <span>address : </span><input bind:value={address}><br />
                    <span>startDate : </span><input bind:value={startDate}><br />
                    <span>year : </span><input bind:value={year}><br />
                    <button on:click={() => handleModify()}>Modify</button>
                {/if}
            </li>
        {/each}
    </ul>
</div>
<div>
    {#if $role === "admin"}
        <form on:submit|preventDefault={handleAdd}>
            <br />
            <button type="submit">Add</button>
        </form>
    {/if}
</div>
<div>
    {#if idEnCours=="yolegang"}
        <span>filmName : </span><input bind:value={filmName}><br />
        <span>filmType : </span><input bind:value={filmType}><br />
        <span>filmProducerName : </span><input bind:value={filmProducerName}><br />
        <span>endDate : </span><input bind:value={endDate}><br />
        <span>district : </span><input bind:value={district}><br />
        <span>sourceLocationId : </span><input bind:value={sourceLocationId}><br />
        <span>filmDirectorName : </span><input bind:value={filmDirectorName}><br />
        <span>address : </span><input bind:value={address}><br />
        <span>startDate : </span><input bind:value={startDate}><br />
        <span>year : </span><input bind:value={year}><br />
        <button on:click={() => handleValidate()}>Validate</button>
    {/if}
</div>