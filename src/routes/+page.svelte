
<script>
    import { onMount } from 'svelte';
    import axios from 'axios';

    let projects = [];
    const googleSheetUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTNZFCHMIHoRLd-3Xolmxn-2E4Y_ua9vTtObr6rZqLKu0SVmb9gwfZhrCIZQ3dGpQrwv9vsUUSuv03-/pub?output=csv';

    // Fetch data from Google Sheets
    const fetchProjects = async () => {
        try {
            const response = await axios.get(googleSheetUrl);
            const csvData = response.data;
            parseCSV(csvData);
        } catch (error) {
            console.error('Error fetching projects:', error);
        }
    };

    // Parse CSV into a JavaScript object
    const parseCSV = (csv) => {
        const rows = csv.split('\n');
        const headers = rows[0].split(',');
        projects = rows.slice(1).map((row) => {
            const values = row.split(',');
            return headers.reduce((acc, header, index) => {
                acc[header.trim()] = values[index]?.trim();
                return acc;
            }, {});
        });
    };

    onMount(() => {
        fetchProjects();
    });

    let hoveredProject = null;
    let expanded = false; // State for description expansion

    const handleMouseEnter = (project) => {
        hoveredProject = project;
    };

    const handleMouseLeave = () => {
        hoveredProject = null;
        expanded = false;
    };

    const toggleDescription = () => {
        expanded = !expanded;
    };
</script>

<style>
    .layout {
        display: flex;
        min-height: 100vh;
        flex-direction: column;
    }

    .content-wrapper {
        display: flex;
        flex: 1;
        overflow: hidden;
    }

    .left-column {
        width: 25%; 
        background-color: #f8f9fa; /* Light gray background */
        padding: 20px;
        box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
        font-family: system-ui, sans-serif;
    }

    .left-column h1 {
        font-size: 24px;
        margin-bottom: 10px;
    }

    .left-column p {
        margin-bottom: 20px;
        line-height: 1.6;
    }

    .main-content {
        flex: 1;
        position: relative;
        overflow: hidden;
    }

    footer {
        text-align: center;
        padding: 10px;
        background-color: #333;
        color: #fff;
        font-size: 14px;
        font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    }

    /* Dot styles */
    .dot {
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background-color: #0d00ff;
        position: absolute;
        cursor: pointer;
        transition: transform 0.2s ease;
    }
    .dot:hover {
        transform: scale(1.5);
    }

    .line {
        position: absolute;
        width: 1px;
        height: 2px;
        background-color: #333;
        transform-origin: 0 50%; /* Rotate from the start of the line */
        z-index: 9;
    }
    

    .info-box {
        position: absolute;
        background: #fff;
        border: 1px solid #ddd;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        padding: 15px;
        max-width: 300px;
        z-index: 10;
        font-family: system-ui, sans-serif;
        font-size: 14px;
    }

    .info-box img {
        width: 100%;
        height: auto;
        border-radius: 8px;
        margin-bottom: 10px;
    }

    .info-box h3 {
        margin: 0 0 10px;
    }

    .info-box p {
        margin: 0 0 10px;
        overflow: hidden;
        display: -webkit-box;
        -webkit-line-clamp: 3;
        -webkit-box-orient: vertical;
        text-overflow: ellipsis;
    }

    .info-box p.expanded {
        -webkit-line-clamp: unset;
        overflow: visible;
    }

    .info-box a {
        color: #007bff;
        text-decoration: none;
    }

    .info-box a:hover {
        text-decoration: underline;
    }

    /* .tooltip {
        position: absolute;
        background: #afafaf;
        color: #fff;
        padding: 5px 10px;
        border-radius: 4px;
        font-size: 14px;
        pointer-events: none;
        white-space: nowrap;
        opacity: 0;
        transform: translate(-50%, -150%);
        transition: opacity 0.2s ease;
        font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    }
    .tooltip.visible {
        opacity: 1;
    }
 */

</style>

<div class="layout">
    <!-- Main content area -->
    <div class="content-wrapper">
        <!-- Static left column -->
        <aside class="left-column">
            <h1>turi</h1>
            <p>I consider myself a researcher, creative computer designer and artist from Chile with a keen interest in the potential of translation of creative technology and the Natural world. As a process-oriented practitioner I aim to explore new interactions towards environmental, cultural, and social sustainability through the power of imagination, “data-stories” and popular culture. The current environmental crisis is a call to move from methods focused on individuals to ones oriented to the ecosystem, emphasising collaboration and cooperation over domination and exploitation.</p>
            <p>
                <strong>Contact:</strong> <br />
                Email: <a href="mailto:antoturi.valencia@gmail.com">antoturi.valencia@gmail.com</a> <br />
                Phone: +123 456 789
            </p>
        </aside>

        <!-- Main content area -->        
        <main class="main-content">
            {#each projects as project (project.ID)}
                <div
                    class="dot"
                    style="top: {Math.random() * 90}vh; left: {33.33 + Math.random() * 66.67}vw;"
                    on:mouseenter={() => handleMouseEnter(project)}
                    
                    aria-label="{project.Name}"
                ></div>
            {/each}

            {#if hoveredProject}
                <div
                    class="info-box"
                    style="top: {hoveredProject.top}px; left: {hoveredProject.left}px; transform: translate(60%, 50%);"
                >
                <img src="{hoveredProject.ImageURL}" alt="{hoveredProject.Name}" />
                <h3>{hoveredProject.Name}</h3>
                <p class:expanded={expanded}>{hoveredProject.Description}</p>
                <button on:click={toggleDescription}>
                    {expanded ? 'Show Less' : 'Read More'}
                </button>
                {#if hoveredProject.Link}
                    <p><a href="{hoveredProject.Link}" target="_blank">View Project</a></p>
                {/if}
            </div>
        {/if}
    </main>
</div>



    <!-- Footer -->
    <footer>
        Made with ❤️ and help from my friends.
    </footer>
</div>
