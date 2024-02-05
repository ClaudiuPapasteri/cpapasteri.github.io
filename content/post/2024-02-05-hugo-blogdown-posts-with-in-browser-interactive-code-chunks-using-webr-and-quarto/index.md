---
title: Hugo blogdown posts with in-browser interactive code chunks using webR and Quarto
author: Claudiu C. Papasteri
date: '2024-02-05'
slug: hugo-blogdown-interactive-code-chunk-webr-quarto
categories:
  - R
  - RStudio
tags:
  - R
  - Rstudio
  - Quarto
  - WebR
subtitle: ''
summary: ''
authors: []
lastmod: '2024-02-05T11:50:15+02:00'
featured: yes
format:
  hugo-md:
    code-fold: false
execute: 
  warning: false
engine: knitr
webr: 
  show-startup-message: false    # Disable displaying status of webR initialization
  packages: ["glue", "tidyr"]    # Install R packages on document open
filters:
  - webr  
---

  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/monaco-editor@0.45.0/min/vs/editor/editor.main.css" />
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" integrity="sha512-DTOQO9RWCH3ppGqcWaEA1BIZOC6xxalwEsw9c2QQeAIftl+Vegovlnee1c9QX4TctnWMn13TZye+giMm8e2LwA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
  

<style type="text/css">
.monaco-editor pre {
  background-color: unset !important;
}

.qwebr-editor-toolbar {
  width: 100%;
  display: flex;
  justify-content: space-between;
  box-sizing: border-box;
}

.qwebr-editor-toolbar-left-buttons, .qwebr-editor-toolbar-right-buttons {
  display: flex;
}

.qwebr-non-interactive-loading-container.qwebr-cell-needs-evaluation, .qwebr-non-interactive-loading-container.qwebr-cell-evaluated {
  justify-content: center;
  display: flex;
  background-color: rgba(250, 250, 250, 0.65);
  border: 1px solid rgba(233, 236, 239, 0.65);
  border-radius: 0.5rem;
  margin-top: 15px;
  margin-bottom: 15px;
}

.qwebr-r-project-logo {
  color: #2767B0; /* R Project's blue color */
}

.qwebr-icon-status-spinner {
  color: #7894c4;
}

.qwebr-icon-run-code {
  color: #0d9c29
}

.qwebr-output-code-stdout {
  color: #111;
}

.qwebr-output-code-stderr {
  color: #db4133;
}

.qwebr-editor {
  border: 1px solid #EEEEEE;
}

.qwebr-editor-toolbar {
  background-color: #EEEEEE;
  padding: 0.2rem 0.5rem;
}

.qwebr-button {
  background-color: #EEEEEE;
  display: inline-block;
  font-weight: 400;
  line-height: 1;
  text-decoration: none;
  text-align: center;
  color: #000;
  border-color: #dee2e6;
  border: 1px solid rgba(0,0,0,0);
  padding: 0.375rem 0.75rem;
  font-size: .9rem;
  border-radius: 0.25rem;
  transition: color .15s ease-in-out,background-color .15s ease-in-out,border-color .15s ease-in-out,box-shadow .15s ease-in-out;
}

.qwebr-button:hover {
  color: #000;
  background-color: #d9dce0;
  border-color: #c8ccd0;
}

.qwebr-button:disabled,.qwebr-button.disabled,fieldset:disabled .qwebr-button {
  pointer-events: none;
  opacity: .65
}

.qwebr-button-reset {
  color: #696969; /*#4682b4;*/
}

.qwebr-button-copy {
  color: #696969;
}


/* Custom styling for RevealJS Presentations*/

/* Reset the style of the interactive area */
.reveal div.qwebr-interactive-area {
  display: block;
  box-shadow: none;
  max-width: 100%;
  max-height: 100%;
  margin: 0;
  padding: 0;
} 

/* Provide space to entries */
.reveal div.qwebr-output-code-area pre div {
  margin: 1px 2px 1px 10px;
}

/* Collapse the inside code tags to avoid extra space between line outputs */
.reveal pre div code.qwebr-output-code-stdout, .reveal pre div code.qwebr-output-code-stderr {
  padding: 0;
  display: contents;
}

.reveal pre div code.qwebr-output-code-stdout {
  color: #111;
}

.reveal pre div code.qwebr-output-code-stderr {
  color: #db4133;
}


/* Create a border around console and output (does not effect graphs) */
.reveal div.qwebr-console-area {
  border: 1px solid #EEEEEE;
  box-shadow: 2px 2px 10px #EEEEEE;
}

/* Cap output height and allow text to scroll */
/* TODO: Is there a better way to fit contents/max it parallel to the monaco editor size? */
.reveal div.qwebr-output-code-area pre {
  max-height: 400px;
  overflow: scroll;
}

</style>

<script type="module">
// Document level settings ----

// Determine if we need to install R packages
globalThis.qwebrInstallRPackagesList = ['glue', 'tidyr'];

// Specify possible locations to search for the repository
globalThis.qwebrPackageRepoURLS = ['https://repo.r-wasm.org/'];

// Check to see if we have an empty array, if we do set to skip the installation.
globalThis.qwebrSetupRPackages = !(qwebrInstallRPackagesList.indexOf("") !== -1);
globalThis.qwebrAutoloadRPackages = true;

// Display a startup message?
globalThis.qwebrShowStartupMessage = false;
globalThis.qwebrShowHeaderMessage = false;

// Describe the webR settings that should be used
globalThis.qwebrCustomizedWebROptions = {
  "baseURL": "https://webr.r-wasm.org/v0.2.2/",
  "serviceWorkerUrl": "",
  "homedir": "/home/web_user", 
  "channelType": "ChannelType.Automatic"
};

// Store cell data
globalThis.qwebrCellDetails = [{"id":1,"options":{"classes":"","warning":"true","output":"true","label":"unnamed-chunk-1","results":"markup","dpi":72,"fig-height":5,"out-height":"","fig-width":7,"fig-cap":"","out-width":"700px","message":"true","comment":"","context":"interactive"},"code":"message <- c(\n  \"I ran the code chunk\",\n  \"I already knew what was going to happen\",\n  \"I still wanted to test it\"\n)\n\nsample(message, size = 1)"},{"id":2,"options":{"classes":"","warning":"true","output":"false","label":"unnamed-chunk-2","results":"markup","dpi":72,"fig-height":5,"out-height":"","fig-width":7,"fig-cap":"","out-width":"700px","message":"true","comment":"","context":"interactive"},"code":"webr::shim_install()   # so we can use install.packages() instead of webr::install()\ninstall.packages(\"ggplot2\", quiet = TRUE) "},{"id":3,"options":{"classes":"","warning":"true","output":"true","label":"unnamed-chunk-3","results":"markup","dpi":72,"fig-height":5,"out-height":"","fig-width":7,"fig-cap":"","out-width":"700px","message":"true","comment":"","context":"interactive"},"code":"library(\"ggplot2\")\n\nggplot(iris, aes(Sepal.Length, Petal.Length, colour = Species)) + \n  geom_point()"},{"id":4,"options":{"classes":"","warning":"true","output":"true","label":"unnamed-chunk-4","results":"markup","dpi":72,"fig-height":5,"out-height":"","fig-width":7,"fig-cap":"","out-width":"700px","message":"true","comment":"","context":"interactive"},"code":"1 + "}];

</script>

<script type="module">
// Declare startupMessageQWebR globally
globalThis.qwebrStartupMessage = document.createElement("p");


// Function to set the button text
globalThis.qwebrSetInteractiveButtonState = function(buttonText, enableCodeButton = true) {
  document.querySelectorAll(".qwebr-button-run").forEach((btn) => {
    btn.innerHTML = buttonText;
    btn.disabled = !enableCodeButton;
  });
}

// Function to update the status message in non-interactive cells
globalThis.qwebrUpdateStatusMessage = function(message) {
  document.querySelectorAll(".qwebr-status-text.qwebr-cell-needs-evaluation").forEach((elem) => {
    elem.innerText = message;
  });
}

// Function to update the status message
globalThis.qwebrUpdateStatusHeader = function(message) {
  qwebrStartupMessage.innerHTML = `
    <i class="fa-solid fa-spinner fa-spin qwebr-icon-status-spinner"></i>
    <span>${message}</span>`;
}

// Function that attaches the document status message and diagnostics
function displayStartupMessage(showStartupMessage, showHeaderMessage) {
  if (!showStartupMessage) {
    return;
  }

  // Get references to header elements
  const headerHTML = document.getElementById("title-block-header");
  const headerRevealJS = document.getElementById("title-slide");

  // Create the outermost div element for metadata
  const quartoTitleMeta = document.createElement("div");
  quartoTitleMeta.classList.add("quarto-title-meta");

  // Create the first inner div element
  const firstInnerDiv = document.createElement("div");
  firstInnerDiv.setAttribute("id", "qwebr-status-message-area");

  // Create the second inner div element for "WebR Status" heading and contents
  const secondInnerDiv = document.createElement("div");
  secondInnerDiv.setAttribute("id", "qwebr-status-message-title");
  secondInnerDiv.classList.add("quarto-title-meta-heading");
  secondInnerDiv.innerText = "WebR Status";

  // Create another inner div for contents
  const secondInnerDivContents = document.createElement("div");
  secondInnerDivContents.setAttribute("id", "qwebr-status-message-body");
  secondInnerDivContents.classList.add("quarto-title-meta-contents");

  // Describe the WebR state
  qwebrStartupMessage.innerText = "🟡 Loading...";
  qwebrStartupMessage.setAttribute("id", "qwebr-status-message-text");
  // Add `aria-live` to auto-announce the startup status to screen readers
  qwebrStartupMessage.setAttribute("aria-live", "assertive");

  // Append the startup message to the contents
  secondInnerDivContents.appendChild(qwebrStartupMessage);

  // Add a status indicator for COOP and COEP Headers if needed
  if (showHeaderMessage) {
    const crossOriginMessage = document.createElement("p");
    crossOriginMessage.innerText = `${crossOriginIsolated ? '🟢' : '🟡'} COOP & COEP Headers`;
    crossOriginMessage.setAttribute("id", "qwebr-coop-coep-header");
    secondInnerDivContents.appendChild(crossOriginMessage);
  }

  // Combine the inner divs and contents
  firstInnerDiv.appendChild(secondInnerDiv);
  firstInnerDiv.appendChild(secondInnerDivContents);
  quartoTitleMeta.appendChild(firstInnerDiv);

  // Determine where to insert the quartoTitleMeta element
  if (headerHTML || headerRevealJS) {
    // Append to the existing "title-block-header" element or "title-slide" div
    (headerHTML || headerRevealJS).appendChild(quartoTitleMeta);
  } else {
    // If neither headerHTML nor headerRevealJS is found, insert after "webr-monaco-editor-init" script
    const monacoScript = document.getElementById("qwebr-monaco-editor-init");
    const header = document.createElement("header");
    header.setAttribute("id", "title-block-header");
    header.appendChild(quartoTitleMeta);
    monacoScript.after(header);
  }
}

displayStartupMessage(qwebrShowStartupMessage, qwebrShowHeaderMessage);
</script>

<script type="module">
// Supported Evaluation Types for Context
globalThis.EvalTypes = Object.freeze({
  Interactive: 'interactive',
  Setup: 'setup',
  Output: 'output',
});

// Function that dispatches the creation request
globalThis.qwebrCreateHTMLElement = function (
  cellData
) {

  // Extract key components
  const evalType = cellData.options.context;
  const qwebrCounter = cellData.id;

  // We make an assumption that insertion points are defined by the Lua filter as:
  // qwebr-insertion-location-{qwebrCounter} 
  const elementLocator = document.getElementById(`qwebr-insertion-location-${qwebrCounter}`);

  // Figure out the routine to use to insert the element.
  let qwebrElement;
  switch ( evalType ) {
    case EvalTypes.Interactive:
      qwebrElement = qwebrCreateInteractiveElement(qwebrCounter, cellData.options);
      break;
    case EvalTypes.Output: 
      qwebrElement = qwebrCreateNonInteractiveOutputElement(qwebrCounter, cellData.options);
      break;
    case EvalTypes.Setup: 
      qwebrElement = qwebrCreateNonInteractiveSetupElement(qwebrCounter, cellData.options);
      break;
    default: 
      qwebrElement = document.createElement('div');
      qwebrElement.textContent = 'Error creating `quarto-webr` element';
  }

  // Insert the dynamically generated object at the document location.
  elementLocator.appendChild(qwebrElement);
};

// Function that setups the interactive element creation
globalThis.qwebrCreateInteractiveElement = function (qwebrCounter, qwebrOptions) {

  // Create main div element
  var mainDiv = document.createElement('div');
  mainDiv.id = 'qwebr-interactive-area-' + qwebrCounter;
  mainDiv.className = `qwebr-interactive-area`;
  if (qwebrOptions.classes) {
    mainDiv.className += " " + qwebrOptions.classes
  }

  // Add a unique cell identifier that users can customize
  if (qwebrOptions.label) {
    mainDiv.setAttribute('data-id', qwebrOptions.label);
  }

  // Create toolbar div
  var toolbarDiv = document.createElement('div');
  toolbarDiv.className = 'qwebr-editor-toolbar';
  toolbarDiv.id = 'qwebr-editor-toolbar-' + qwebrCounter;

  // Create a div to hold the left buttons
  var leftButtonsDiv = document.createElement('div');
  leftButtonsDiv.className = 'qwebr-editor-toolbar-left-buttons';

  // Create a div to hold the right buttons
  var rightButtonsDiv = document.createElement('div');
  rightButtonsDiv.className = 'qwebr-editor-toolbar-right-buttons';

  // Create Run Code button
  var runCodeButton = document.createElement('button');
  runCodeButton.className = 'btn btn-default qwebr-button qwebr-button-run';
  runCodeButton.disabled = true;
  runCodeButton.type = 'button';
  runCodeButton.id = 'qwebr-button-run-' + qwebrCounter;
  runCodeButton.textContent = '🟡 Loading webR...';
  runCodeButton.title = `Run code (Shift + Enter)`;

  // Append buttons to the leftButtonsDiv
  leftButtonsDiv.appendChild(runCodeButton);

  // Create Reset button
  var resetButton = document.createElement('button');
  resetButton.className = 'btn btn-light btn-xs qwebr-button qwebr-button-reset';
  resetButton.type = 'button';
  resetButton.id = 'qwebr-button-reset-' + qwebrCounter;
  resetButton.title = 'Start over';
  resetButton.innerHTML = '<i class="fa-solid fa-arrows-rotate"></i>';

  // Create Copy button
  var copyButton = document.createElement('button');
  copyButton.className = 'btn btn-light btn-xs qwebr-button qwebr-button-copy';
  copyButton.type = 'button';
  copyButton.id = 'qwebr-button-copy-' + qwebrCounter;
  copyButton.title = 'Copy code';
  copyButton.innerHTML = '<i class="fa-regular fa-copy"></i>';

  // Append buttons to the rightButtonsDiv
  rightButtonsDiv.appendChild(resetButton);
  rightButtonsDiv.appendChild(copyButton);

  // Create console area div
  var consoleAreaDiv = document.createElement('div');
  consoleAreaDiv.id = 'qwebr-console-area-' + qwebrCounter;
  consoleAreaDiv.className = 'qwebr-console-area';

  // Create editor div
  var editorDiv = document.createElement('div');
  editorDiv.id = 'qwebr-editor-' + qwebrCounter;
  editorDiv.className = 'qwebr-editor';

  // Create output code area div
  var outputCodeAreaDiv = document.createElement('div');
  outputCodeAreaDiv.id = 'qwebr-output-code-area-' + qwebrCounter;
  outputCodeAreaDiv.className = 'qwebr-output-code-area';
  outputCodeAreaDiv.setAttribute('aria-live', 'assertive');

  // Create pre element inside output code area
  var preElement = document.createElement('pre');
  preElement.style.visibility = 'hidden';
  outputCodeAreaDiv.appendChild(preElement);

  // Create output graph area div
  var outputGraphAreaDiv = document.createElement('div');
  outputGraphAreaDiv.id = 'qwebr-output-graph-area-' + qwebrCounter;
  outputGraphAreaDiv.className = 'qwebr-output-graph-area';

  // Append buttons to the toolbar
  toolbarDiv.appendChild(leftButtonsDiv);
  toolbarDiv.appendChild(rightButtonsDiv);

  // Append all elements to the main div
  mainDiv.appendChild(toolbarDiv);
  consoleAreaDiv.appendChild(editorDiv);
  consoleAreaDiv.appendChild(outputCodeAreaDiv);
  mainDiv.appendChild(consoleAreaDiv);
  mainDiv.appendChild(outputGraphAreaDiv);

  return mainDiv;
}

// Function that adds output structure for non-interactive output
globalThis.qwebrCreateNonInteractiveOutputElement = function(qwebrCounter, qwebrOptions) {
  // Create main div element
  var mainDiv = document.createElement('div');
  mainDiv.id = 'qwebr-noninteractive-area-' + qwebrCounter;
  mainDiv.className = `qwebr-noninteractive-area`;
  if (qwebrOptions.classes) {
    mainDiv.className += " " + qwebrOptions.classes
  }
  
  // Add a unique cell identifier that users can customize
  if (qwebrOptions.label) {
    mainDiv.setAttribute('data-id', qwebrOptions.label);
  }
  
  // Create a status container div
  var statusContainer = createLoadingContainer(qwebrCounter);

  // Create output code area div
  var outputCodeAreaDiv = document.createElement('div');
  outputCodeAreaDiv.id = 'qwebr-output-code-area-' + qwebrCounter;
  outputCodeAreaDiv.className = 'qwebr-output-code-area';
  outputCodeAreaDiv.setAttribute('aria-live', 'assertive');

  // Create pre element inside output code area
  var preElement = document.createElement('pre');
  preElement.style.visibility = 'hidden';
  outputCodeAreaDiv.appendChild(preElement);

  // Create output graph area div
  var outputGraphAreaDiv = document.createElement('div');
  outputGraphAreaDiv.id = 'qwebr-output-graph-area-' + qwebrCounter;
  outputGraphAreaDiv.className = 'qwebr-output-graph-area';

  // Append all elements to the main div
  mainDiv.appendChild(statusContainer);
  mainDiv.appendChild(outputCodeAreaDiv);
  mainDiv.appendChild(outputGraphAreaDiv);

  return mainDiv;
};

// Function that adds a stub in the page to indicate a setup cell was used.
globalThis.qwebrCreateNonInteractiveSetupElement = function(qwebrCounter, qwebrOptions) {
  // Create main div element
  var mainDiv = document.createElement('div');
  mainDiv.id = `qwebr-noninteractive-setup-area-${qwebrCounter}`;
  mainDiv.className = `qwebr-noninteractive-setup-area`;
  if (qwebrOptions.classes) {
    mainDiv.className += " " + qwebrOptions.classes
  }


  // Add a unique cell identifier that users can customize
  if (qwebrOptions.label) {
    mainDiv.setAttribute('data-id', qwebrOptions.label);
  }

  // Create a status container div
  var statusContainer = createLoadingContainer(qwebrCounter);

  // Append status onto the main div
  mainDiv.appendChild(statusContainer);

  return mainDiv;
}


// Function to create loading container with specified ID
globalThis.createLoadingContainer = function(qwebrCounter) {

  // Create a status container
  const container = document.createElement('div');
  container.id = `qwebr-non-interactive-loading-container-${qwebrCounter}`;
  container.className = 'qwebr-non-interactive-loading-container qwebr-cell-needs-evaluation';

  // Create an R project logo to indicate its a code space
  const rProjectIcon = document.createElement('i');
  rProjectIcon.className = 'fa-brands fa-r-project fa-3x qwebr-r-project-logo';

  // Setup a loading icon from font awesome
  const spinnerIcon = document.createElement('i');
  spinnerIcon.className = 'fa-solid fa-spinner fa-spin fa-1x qwebr-icon-status-spinner';

  // Add a section for status text
  const statusText = document.createElement('p');
  statusText.id = `qwebr-status-text-${qwebrCounter}`;
  statusText.className = `qwebr-status-text qwebr-cell-needs-evaluation`;
  statusText.innerText = 'Loading webR...';

  // Incorporate an inner container
  const innerContainer = document.createElement('div');

  // Append elements to the inner container
  innerContainer.appendChild(spinnerIcon);
  innerContainer.appendChild(statusText);

  // Append elements to the main container
  container.appendChild(rProjectIcon);
  container.appendChild(innerContainer);

  return container;
}
</script>

<script type="module">
// Function to install a single package
async function qwebrInstallRPackage(packageName) {
  await mainWebR.evalRVoid(`webr::install('${packageName}');`);
}

// Function to load a single package
async function qwebrLoadRPackage(packageName) {
  await mainWebR.evalRVoid(`require('${packageName}', quietly = TRUE)`);
}

// Generic function to process R packages
async function qwebrProcessRPackagesWithStatus(packages, processType, displayStatusMessageUpdate = true) {
  // Switch between contexts
  const messagePrefix = processType === 'install' ? 'Installing' : 'Loading';

  // Modify button state
  qwebrSetInteractiveButtonState(`🟡 ${messagePrefix} package ...`, false);

  // Iterate over packages
  for (let i = 0; i < packages.length; i++) {
    const activePackage = packages[i];
    const formattedMessage = `${messagePrefix} package ${i + 1} out of ${packages.length}: ${activePackage}`;

    // Display the update in header
    if (displayStatusMessageUpdate) {
      qwebrUpdateStatusHeader(formattedMessage);
    }

    // Display the update in non-active areas
    qwebrUpdateStatusMessage(formattedMessage);

    // Run package installation
    if (processType === 'install') {
      await qwebrInstallRPackage(activePackage);
    } else {
      await qwebrLoadRPackage(activePackage);
    }
  }

  // Clean slate
  if (processType === 'load') {
    await mainWebR.flush();
  }
}

// Start a timer
const initializeWebRTimerStart = performance.now();

// Encase with a dynamic import statement
globalThis.qwebrInstance = import(qwebrCustomizedWebROptions.baseURL + "webr.mjs").then(
  async ({ WebR, ChannelType }) => {
    // Populate WebR options with defaults or new values based on `webr` meta
    globalThis.mainWebR = new WebR(qwebrCustomizedWebROptions);

    // Initialization WebR
    await mainWebR.init();

    // Setup a shelter
    globalThis.mainWebRCodeShelter = await new mainWebR.Shelter();

    // Setup a pager to allow processing help documentation 
    await mainWebR.evalRVoid('webr::pager_install()'); 

    // Override the existing install.packages() to use webr::install()
    await mainWebR.evalRVoid('webr::shim_install()'); 

    // Specify the repositories to pull from
    // Note: webR does not use the `repos` option, but instead uses `webr_pkg_repos`
    // inside of `install()`. However, other R functions still pull from `repos`.
    await mainWebR.evalRVoid(`
      options(
        webr_pkg_repos = c(${qwebrPackageRepoURLS.map(repoURL => `'${repoURL}'`).join(',')}),
        repos = c(${qwebrPackageRepoURLS.map(repoURL => `'${repoURL}'`).join(',')})
      )
    `);

    // Check to see if any packages need to be installed
    if (qwebrSetupRPackages) {
      // Obtain only a unique list of packages
      const uniqueRPackageList = Array.from(new Set(qwebrInstallRPackagesList));

      // Install R packages one at a time (either silently or with a status update)
      await qwebrProcessRPackagesWithStatus(uniqueRPackageList, 'install', qwebrShowStartupMessage);

      if (qwebrAutoloadRPackages) {
        // Load R packages one at a time (either silently or with a status update)
        await qwebrProcessRPackagesWithStatus(uniqueRPackageList, 'load', qwebrShowStartupMessage);
      }
    }
  }
);

// Stop timer
const initializeWebRTimerEnd = performance.now();

</script>

<script type="module">
// Function to verify a given JavaScript Object is empty
globalThis.qwebrIsObjectEmpty = function (arr) {
    return Object.keys(arr).length === 0;
}

// Global version of the Escape HTML function that converts HTML 
// characters to their HTML entities.
globalThis.qwebrEscapeHTMLCharacters = function(unsafe) {
    return unsafe
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");
  };

// Passthrough results
globalThis.qwebrIdentity = function(x) {
    return x;
};

// Append a comment
globalThis.qwebrPrefixComment = function(x, comment) {
    return `${comment}${x}`;
};

// Function to parse the pager results
globalThis.qwebrParseTypePager = async function (msg) { 

    // Split out the event data
    const { path, title, deleteFile } = msg.data; 

    // Process the pager data by reading the information from disk
    const paged_data = await mainWebR.FS.readFile(path).then((data) => {
        // Obtain the file content
        let content = new TextDecoder().decode(data);

        // Remove excessive backspace characters until none remain
        while(content.match(/.[\b]/)){
        content = content.replace(/.[\b]/g, '');
        }

        // Returned cleaned data
        return content;
    });

    // Unlink file if needed
    if (deleteFile) { 
        await mainWebR.FS.unlink(path); 
    } 

    // Return extracted data with spaces
    return paged_data;
} 

// Function to run the code using webR and parse the output
globalThis.qwebrComputeEngine = async function(
    codeToRun, 
    elements, 
    options) {

    // Call into the R compute engine that persists within the document scope.
    // To be prepared for all scenarios, the following happens: 
    // 1. We setup a canvas device to write to by making a namespace call into the {webr} package
    // 2. We use values inside of the options array to set the figure size.
    // 3. We capture the output stream information (STDOUT and STERR)
    // 4. While parsing the results, we disable image creation.

    // Create a canvas variable for graphics
    let canvas = undefined;

    // Create a pager variable for help/file contents
    let pager = [];

    // Handle how output is processed
    let showMarkup = options.results === "markup" && options.output !== "asis";
    let processOutput;

    if (showMarkup) {
        processOutput = qwebrEscapeHTMLCharacters;
    } else {
        processOutput = qwebrIdentity;
    }

    // ---- 
    // Convert from Inches to Pixels by using DPI (dots per inch)
    // for bitmap devices (dpi * inches = pixels)
    let fig_width = options["fig-width"] * options["dpi"]
    let fig_height = options["fig-height"] * options["dpi"]

    // Initialize webR
    await mainWebR.init();

    // Setup a webR canvas by making a namespace call into the {webr} package
    await mainWebR.evalRVoid(`webr::canvas(width=${fig_width}, height=${fig_height})`);

    const result = await mainWebRCodeShelter.captureR(codeToRun, {
        withAutoprint: true,
        captureStreams: true,
        captureConditions: false//,
        // env: webR.objs.emptyEnv, // maintain a global environment for webR v0.2.0
    });

    // -----

    // Start attempting to parse the result data
    processResultOutput:try {

        // Stop creating images
        await mainWebR.evalRVoid("dev.off()");
        
        // Avoid running through output processing
        if (options.results === "hide" || options.output === "false") { 
            break processResultOutput; 
        }

        // Merge output streams of STDOUT and STDErr (messages and errors are combined.)
        // Require both `warning` and `message` to be true to display `STDErr`. 
        const out = result.output
        .filter(
            evt => evt.type === "stdout" || 
            ( evt.type === "stderr" && (options.warning === "true" && options.message === "true")) 
        )
        .map((evt, index) => {
            const className = `qwebr-output-code-${evt.type}`;
            const outputResult = qwebrPrefixComment(processOutput(evt.data), options.comment);
            return `<code id="${className}-editor-${elements.id}-result-${index + 1}" class="${className}">${outputResult}</code>`;
        })
        .join("\n");


        // Clean the state
        // We're now able to process both graphics and pager events.
        // As a result, we cannot maintain a true 1-to-1 output order 
        // without individually feeding each line
        const msgs = await mainWebR.flush();

        // Output each image event stored
        msgs.forEach((msg) => {
        // Determine if old canvas can be used or a new canvas is required.
        if (msg.type === 'canvas'){
            // Add image to the current canvas
            if (msg.data.event === 'canvasImage') {
                canvas.getContext('2d').drawImage(msg.data.image, 0, 0);
            } else if (msg.data.event === 'canvasNewPage') {

                // Generate a new canvas element
                canvas = document.createElement("canvas");
                canvas.setAttribute("width", 2 * fig_width);
                canvas.setAttribute("height", 2 * fig_height);
                canvas.style.width = options["out-width"] ? options["out-width"] : `${fig_width}px`;
                if (options["out-height"]) {
                    canvas.style.height = options["out-height"];
                }
                canvas.style.display = "block";
                canvas.style.margin = "auto";
            }
        } 
        });

        // Use `map` to process the filtered "pager" events asynchronously
        const pager = await Promise.all(
            msgs.filter(msg => msg.type === 'pager').map(
                async (msg) => {
                    return await qwebrParseTypePager(msg);
                }
            )
        );

        // Nullify the output area of content
        elements.outputCodeDiv.innerHTML = "";
        elements.outputGraphDiv.innerHTML = "";

        // Design an output object for messages
        const pre = document.createElement("pre");
        if (/\S/.test(out)) {
            // Display results as HTML elements to retain output styling
            const div = document.createElement("div");
            div.innerHTML = out;
            pre.appendChild(div);
        } else {
            // If nothing is present, hide the element.
            pre.style.visibility = "hidden";
        }

        elements.outputCodeDiv.appendChild(pre);

        // Place the graphics on the canvas
        if (canvas) {
            // Create figure element
            const figureElement = document.createElement('figure');

            // Append canvas to figure
            figureElement.appendChild(canvas);

            if (options['fig-cap']) {
                // Create figcaption element
                const figcaptionElement = document.createElement('figcaption');
                figcaptionElement.innerText = options['fig-cap'];
                // Append figcaption to figure
                figureElement.appendChild(figcaptionElement);    
            }

            elements.outputGraphDiv.appendChild(figureElement);
        }

        // Display the pager data
        if (pager) {
        // Use the `pre` element to preserve whitespace.
        pager.forEach((paged_data, index) => {
            let pre_pager = document.createElement("pre");
            pre_pager.innerText = paged_data;
            pre_pager.classList.add("qwebr-output-code-pager");
            pre_pager.setAttribute("id", `qwebr-output-code-pager-editor-${elements.id}-result-${index + 1}`);
            elements.outputCodeDiv.appendChild(pre_pager);
        });
        }
    } finally {
        // Clean up the remaining code
        mainWebRCodeShelter.purge();
    }
}

// Function to execute the code (accepts code as an argument)
globalThis.qwebrExecuteCode = async function (
    codeToRun,
    id,
    options = {}) {

    // If options are not passed, we fall back on the bare minimum to handle the computation
    if (qwebrIsObjectEmpty(options)) {
        options = { 
            "context": "interactive", 
            "fig-width": 7, "fig-height": 5, 
            "out-width": "700px", "out-height": "", 
            "dpi": 72,
            "results": "markup", 
            "warning": "true", "message": "true",
        };
    }

    // Next, we access the compute areas values
    const elements = {
        runButton: document.getElementById(`qwebr-button-run-${id}`),
        outputCodeDiv: document.getElementById(`qwebr-output-code-area-${id}`),
        outputGraphDiv: document.getElementById(`qwebr-output-graph-area-${id}`),
        id: id,
    }

    // Disallowing execution of other code cells
    document.querySelectorAll(".qwebr-button-run").forEach((btn) => {
        btn.disabled = true;
    });

    if (options.context == EvalTypes.Interactive) {
        // Emphasize the active code cell
        elements.runButton.innerHTML = '<i class="fa-solid fa-spinner fa-spin qwebr-icon-status-spinner"></i> <span>Run Code</span>';
    }

    // Evaluate the code and parse the output into the document
    await qwebrComputeEngine(codeToRun, elements, options);

    // Switch to allowing execution of code
    document.querySelectorAll(".qwebr-button-run").forEach((btn) => {
        btn.disabled = false;
    });

    if (options.context == EvalTypes.Interactive) {
        // Revert to the initial code cell state
        elements.runButton.innerHTML = '<i class="fa-solid fa-play qwebr-icon-run-code"></i> <span>Run Code</span>';
    }
}

</script>

<script src="https://cdn.jsdelivr.net/npm/monaco-editor@0.45.0/min/vs/loader.js"></script>
<script type="module" id="qwebr-monaco-editor-init">

  // Configure the Monaco Editor's loader
  require.config({
    paths: {
      'vs': 'https://cdn.jsdelivr.net/npm/monaco-editor@0.45.0/min/vs'
    }
  });
</script>

<script type="module">
// Global dictionary to store Monaco Editor instances
globalThis.qwebrEditorInstances = {};

// Function that builds and registers a Monaco Editor instance    
globalThis.qwebrCreateMonacoEditorInstance = function (cellData) {

  const initialCode = cellData.code;
  const qwebrCounter = cellData.id;
  const qwebrOptions = cellData.options;

  // Retrieve the previously created document elements
  let runButton = document.getElementById(`qwebr-button-run-${qwebrCounter}`);
  let resetButton = document.getElementById(`qwebr-button-reset-${qwebrCounter}`);
  let copyButton = document.getElementById(`qwebr-button-copy-${qwebrCounter}`);
  let editorDiv = document.getElementById(`qwebr-editor-${qwebrCounter}`);
  
  // Load the Monaco Editor and create an instance
  let editor;
  require(['vs/editor/editor.main'], function () {
    editor = monaco.editor.create(editorDiv, {
      value: initialCode,
      language: 'r',
      theme: 'vs-light',
      automaticLayout: true,           // Works wonderfully with RevealJS
      scrollBeyondLastLine: false,
      minimap: {
        enabled: false
      },
      fontSize: '17.5pt',              // Bootstrap is 1 rem
      renderLineHighlight: "none",     // Disable current line highlighting
      hideCursorInOverviewRuler: true  // Remove cursor indictor in right hand side scroll bar
    });

    // Store the official counter ID to be used in keyboard shortcuts
    editor.__qwebrCounter = qwebrCounter;

    // Store the official div container ID
    editor.__qwebrEditorId = `qwebr-editor-${qwebrCounter}`;

    // Store the initial code value and options
    editor.__qwebrinitialCode = initialCode;
    editor.__qwebrOptions = qwebrOptions;

    // Set at the model level the preferred end of line (EOL) character to LF.
    // This prevent `\r\n` from being given to the webR engine if the user is on Windows.
    // See details in: https://github.com/coatless/quarto-webr/issues/94
    // Associated error text: 
    // Error: <text>:1:7 unexpected input

    // Retrieve the underlying model
    const model = editor.getModel();
    // Set EOL for the model
    model.setEOL(monaco.editor.EndOfLineSequence.LF);

    // Dynamically modify the height of the editor window if new lines are added.
    let ignoreEvent = false;
    const updateHeight = () => {
      const contentHeight = editor.getContentHeight();
      // We're avoiding a width change
      //editorDiv.style.width = `${width}px`;
      editorDiv.style.height = `${contentHeight}px`;
      try {
        ignoreEvent = true;

        // The key to resizing is this call
        editor.layout();
      } finally {
        ignoreEvent = false;
      }
    };

    // Helper function to check if selected text is empty
    function isEmptyCodeText(selectedCodeText) {
      return (selectedCodeText === null || selectedCodeText === undefined || selectedCodeText === "");
    }

    // Registry of keyboard shortcuts that should be re-added to each editor window
    // when focus changes.
    const addWebRKeyboardShortCutCommands = () => {
      // Add a keydown event listener for Shift+Enter to run all code in cell
      editor.addCommand(monaco.KeyMod.Shift | monaco.KeyCode.Enter, () => {

        // Retrieve all text inside the editor
        qwebrExecuteCode(editor.getValue(), editor.__qwebrCounter, editor.__qwebrOptions);
      });

      // Add a keydown event listener for CMD/Ctrl+Enter to run selected code
      editor.addCommand(monaco.KeyMod.CtrlCmd | monaco.KeyCode.Enter, () => {

        // Get the selected text from the editor
        const selectedText = editor.getModel().getValueInRange(editor.getSelection());
        // Check if no code is selected
        if (isEmptyCodeText(selectedText)) {
          // Obtain the current cursor position
          let currentPosition = editor.getPosition();
          // Retrieve the current line content
          let currentLine = editor.getModel().getLineContent(currentPosition.lineNumber);

          // Propose a new position to move the cursor to
          let newPosition = new monaco.Position(currentPosition.lineNumber + 1, 1);

          // Check if the new position is beyond the last line of the editor
          if (newPosition.lineNumber > editor.getModel().getLineCount()) {
            // Add a new line at the end of the editor
            editor.executeEdits("addNewLine", [{
            range: new monaco.Range(newPosition.lineNumber, 1, newPosition.lineNumber, 1),
            text: "\n", 
            forceMoveMarkers: true,
            }]);
          }
          
          // Run the entire line of code.
          qwebrExecuteCode(currentLine, editor.__qwebrCounter, editor.__qwebrOptions);

          // Move cursor to new position
          editor.setPosition(newPosition);
        } else {
          // Code to run when Ctrl+Enter is pressed with selected code
          qwebrExecuteCode(selectedText, editor.__qwebrCounter, editor.__qwebrOptions);
        }
      });
    }

    // Register an on focus event handler for when a code cell is selected to update
    // what keyboard shortcut commands should work.
    // This is a workaround to fix a regression that happened with multiple
    // editor windows since Monaco 0.32.0 
    // https://github.com/microsoft/monaco-editor/issues/2947
    editor.onDidFocusEditorText(addWebRKeyboardShortCutCommands);

    // Register an on change event for when new code is added to the editor window
    editor.onDidContentSizeChange(updateHeight);

    // Manually re-update height to account for the content we inserted into the call
    updateHeight();

    // Store the editor instance in the global dictionary
    qwebrEditorInstances[editor.__qwebrCounter] = editor;

  });

  // Add a click event listener to the run button
  runButton.onclick = function () {
    qwebrExecuteCode(editor.getValue(), editor.__qwebrCounter, editor.__qwebrOptions);
  };

  // Add a click event listener to the reset button
  copyButton.onclick = function () {
    // Retrieve current code data
    const data = editor.getValue();
    
    // Write code data onto the clipboard.
    navigator.clipboard.writeText(data || "");
  };
  
  // Add a click event listener to the copy button
  resetButton.onclick = function () {
    editor.setValue(editor.__qwebrinitialCode);
  };
  
}
</script>

Running code in the browser is a great tool for education and basic reproducibility.

<div id="qwebr-insertion-location-1"></div>
<noscript>Please enable JavaScript to experience the dynamic code cell content on this page.</noscript>

This is quite simple to accomplish by just enclosing code chunks with `{webr-r}` instead of `{r}` in a Quarto document. Everything works by the magic of WebR that brings R inside your browser with the help of WebAssembly.

At the time of writing WebR contains about 20000 R packages built for WebAssembly (check out the [webr package list](https://repo.r-wasm.org/)).

WebR works with Quarto's `.qmd` documents by virtue of the `quarto-webr` Quarto extension
(check out its [documentation website](https://quarto-webr.thecoatlessprofessor.com/) or [GitHub Repository](https://github.com/coatless/quarto-webr)).

The issue here lies with the fact that my blog doesn't use Quarto `.qmd` but Rmarkdown `.Rmd` files within a [`{blogdown}`](https://bookdown.org/yihui/blogdown/) blog that uses [Hugo](https://gohugo.io/) as site generator. The second issue is that I don't want to switch my blog to Quarto.

Only one thing remains to be done - me showing you how to reproduce this blog post that uses in-browser executable `{webr-r}` chunks within a `.qmd` Quarto document that is rendered within a [`{blogdown}`](https://bookdown.org/yihui/blogdown/) blog that uses [Hugo](https://gohugo.io/) as site generator.

Here we go in 3 easy steps:

### 1. Update blog config file

We need to make change to our `{blogdown}` config file to be able to use Quarto `.qmd` documents with Hugo (more info in [docs](https://quarto.org/docs/output-formats/hugo.html)).

A single change was needed to [my `config.yaml`](https://github.com/ClaudiuPapasteri/cpapasteri.github.io/commit/918f566d53fbc51e332f8d46c9b01442ed6806aa) were I added three extensions to the other `ignoreFiles`:

``` r
ignoreFiles: [\.qmd$, \.ipynb$, \.py$]
```

The only other change to the `config.yaml` would be to set Hugo's markdown renderer to allow raw HTML:

``` r
markup:
  goldmark:
    renderer:
      unsafe: true
```

But many Hugo theme already have this by default. For example, I already had the following lines:

``` r
markup:
  _merge: deep
  goldmark:
    renderer:
      unsafe: true
```

### 2. Create a Quarto blog post

At the moment, `{blogdown}` does not directly support Quarto and `.qmd` documents, but we can still use `blogdown:::new_post()` to issue new posts with `.qmd`. This is in part due to the similarity of Quarto to rmarkdown.

Unfortunately, we cant do this from the `{blogdown}` addin (i.e. `blogdown:::new_post_addin()`).

For this blog post, I ran:

``` r
blogdown::new_post(
  title = "Hugo blogdown posts with in-browser interactive code chunks using webR and Quarto", 
  slug = "hugo-blogdown-interactive-code-chunk-webr-quarto",
  categories = c("R", "RStudio", "Rblog"),          
  tags = c("R", "Rstudio", "Quarto"),
  ext = ".qmd"
)
```

#### Yaml header

Now, we just need to update the yaml header to match Quarto. For this blog post, I used the following, but be sure to adapt it to your own:

``` yaml
---
title: Hugo blogdown posts with in-browser interactive code chunks using webR and Quarto
author: Claudiu C. Papasteri
date: '2024-02-05'
slug: hugo-blogdown-interactive-code-chunk-webr-quarto
categories:
  - R
  - RStudio
tags:
  - R
  - Rstudio
  - Quarto
  - WebR
subtitle: ''
summary: ''
authors: []
lastmod: '2024-02-05T11:50:15+02:00'
featured: yes
format:
  hugo-md:
    code-fold: false
execute: 
  warning: false
---
```

#### Figures

Hugo shortcodes and Quarto shortcodes share the same basic syntax, so collisions are possible but not frequent as shortcodes not recognized by Quarto are passed through unmodified to Hugo.

There are multiple ways to deal with this describred in the [offical docs](https://quarto.org/docs/output-formats/hugo.html) but the safe way is to use a markdown raw block around the entire construct.

For example, to comply with [R-bloggers](https://www.r-bloggers.com/) requirements for relative links in RSS feed using my old rmarkdown posts I needed to reference `foo.png` this way: `![]({{< blogdown/postref >}}foo.png)`. The same would work in most cases with Quarto, but the safe way is:

``` default
`![foo image within Quarto within Hugo site]({{< blogdown/postref >}}foo.png)`{=markdown}
```

![foo image within Quarto within Hugo site]({{< blogdown/postref >}}foo.png)

#### Preview the Quarto document

1.  Ctrl+Shit+K (or Render button in RStudio) to render to an `.md` file of special format `hugo-md`.
2.  Then `blogdown::serve_site()` to start the server for previewing.

> **Note**
>
> In some cases you may want to build the html before previewing: `blogdown::build_site(build_rmd = ".../content/post/.../index.md")`

The above message is obtained by using a [callout block](https://quarto.org/docs/authoring/callouts.html) and this one has the clear distinctive design of Quarto. So if you are seeing it, it means you are going in the right direction.

### 3. Extending the Quarto document with WebR

Install the `quarto-webr` extension in the folder you have the `.qmd` in. Because we are using Hugo, we need a special version that works with `hugo-md`, not the regular version (see [here](https://github.com/coatless/quarto-webr/issues/150)).

``` r
cd .../content/post/.../                       # cd into post folder
# quarto add coatless/quarto-webr --no-prompt    # this would install the regular quarto-webr extension
quarto add coatless/quarto-webr@test-hugo-md --no-prompt  # but we need this one that works with Hugo  
```

Add the following lines to the `yaml` header you already have:

``` yaml
engine: knitr
webr: 
  show-startup-message: false    # Disable displaying status of webR initialization
  packages: ["glue", "tidyr"]    # Install R packages on document open
filters:
  - webr
```

Observe that I already provided within the `yaml` that `{glue}` and `{tidyr}` should be installed on document open. Other packages we may easily install interactively in a familiar way:

<div id="qwebr-insertion-location-2"></div>
<noscript>Please enable JavaScript to experience the dynamic code cell content on this page.</noscript>

Packages are sourced and code runs as you would expect. Click run to see a `ggplot2` plot of the traditional iris dataset:

<div id="qwebr-insertion-location-3"></div>
<noscript>Please enable JavaScript to experience the dynamic code cell content on this page.</noscript>

The extremely cool part is that you can use the interactive code cells to write your own code within. Try it out:

<div id="qwebr-insertion-location-4"></div>
<noscript>Please enable JavaScript to experience the dynamic code cell content on this page.</noscript>

Check out or reuse the code for [this post](https://github.com/ClaudiuPapasteri/cpapasteri.github.io/tree/main/content/post/2024-02-05-hugo-blogdown-posts-with-in-browser-interactive-code-chunks-using-webr-and-quarto).

<script type="module">
// Handle cell initialization initialization
qwebrCellDetails.map(
  (entry) => {
    // Handle the creation of the element
    qwebrCreateHTMLElement(entry);
    // In the event of interactive, initialize the monaco editor
    if (entry.options.context == EvalTypes.Interactive) {
      qwebrCreateMonacoEditorInstance(entry);
    }
  }
);

// Identify non-interactive cells (in order)
const filteredEntries = qwebrCellDetails.filter(entry => {
  const contextOption = entry.options && entry.options.context;
  return ['output', 'setup'].includes(contextOption);
});

// Condition non-interactive cells to only be run after webR finishes its initialization.
qwebrInstance.then(
  async () => {
    const nHiddenCells = filteredEntries.length;
    var currentHiddenCell = 0;


    // Modify button state
    qwebrSetInteractiveButtonState(`🟡 Running hidden code cells ...`, false);

    // Begin processing non-interactive sections
    // Due to the iteration policy, we must use a for() loop.
    // Otherwise, we would need to switch to using reduce with an empty
    // starting promise
    for (const entry of filteredEntries) {

      // Determine cell being examined
      currentHiddenCell = currentHiddenCell + 1;
      const formattedMessage = `Evaluating hidden cell ${currentHiddenCell} out of ${nHiddenCells}`;

      // Update the document status header
      if (qwebrShowStartupMessage) {
        qwebrUpdateStatusHeader(formattedMessage);
      }

      // Display the update in non-active areas
      qwebrUpdateStatusMessage(formattedMessage);

      // Extract details on the active cell
      const evalType = entry.options.context;
      const cellCode = entry.code;
      const qwebrCounter = entry.id;

      // Disable further global status updates
      const activeContainer = document.getElementById(`qwebr-non-interactive-loading-container-${qwebrCounter}`);
      activeContainer.classList.remove('qwebr-cell-needs-evaluation');
      activeContainer.classList.add('qwebr-cell-evaluated');

      // Update status on the code cell
      const activeStatus = document.getElementById(`qwebr-status-text-${qwebrCounter}`);
      activeStatus.innerText = " Evaluating hidden code cell...";
      activeStatus.classList.remove('qwebr-cell-needs-evaluation');
      activeStatus.classList.add('qwebr-cell-evaluated');

      switch (evalType) {
        case 'output':
          // Run the code in a non-interactive state that is geared to displaying output
          await qwebrExecuteCode(`${cellCode}`, qwebrCounter, entry.options);
          break;
        case 'setup':
          const activeDiv = document.getElementById(`qwebr-noninteractive-setup-area-${qwebrCounter}`);
          //activeDiv.textContent = "Computing hidden webR Startup ...";
          // Run the code in a non-interactive state with all output thrown away
          await mainWebR.evalRVoid(`${cellCode}`);
          //activeDiv.textContent = "";
          break;
        default: 
          break; 
      }
      // Disable visibility
      activeContainer.style.visibility = 'hidden';
      activeContainer.style.display = 'none';
    }
  }
).then(
  () => {
    // Release document status as ready

    if (qwebrShowStartupMessage) {
      qwebrStartupMessage.innerText = "🟢 Ready!"
    }
  
    qwebrSetInteractiveButtonState(
      `<i class="fa-solid fa-play qwebr-icon-run-code"></i> <span>Run Code</span>`, 
      true
    );  
  }
);
</script>