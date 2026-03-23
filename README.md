    
<template if:true={modalisopen}>

    <!-- Close Confirmation Dialog -->
    <c-care_confirmation-dialog
        title={Care_CloseReason.Pop_Up_Header}
        message={Care_CloseReason.Pop_Up_Message}
        cancel-label={Care_CloseReason.CancelLabel}
        confirm-label={Care_CloseReason.ConfirmLabel}
        visible={showCloseIdnvConfirmation}
        name="confirmModal"
        onclosemodalclick={handleConfirmDialog}>
    </c-care_confirmation-dialog>

    <!-- Main Modal -->
    <template if:false={isstepUpApi}>
        <section
            role="dialog"
            tabindex="-1"
            aria-labelledby="modal-heading"
            aria-describedby="idnv-announcement"
            class="slds-modal slds-fade-in-open">

            <div class="slds-modal__container">

                <!-- Screen reader announcement -->
                <div id="idnv-announcement" class="slds-assistive-text">
                    Identity Verification Required
                </div>

                <!-- Header -->
                <header class="slds-modal__header">
                    <h2 id="modal-heading" class="slds-text-heading_medium">
                        Locate Customer
                    </h2>
                </header>

                <!-- Body -->
                <div class="slds-modal__content spinnerPosition" id="modal-content-id-1">

                    <!-- Spinner -->
                    <template if:true={submitSpinner}>
                        <lightning-spinner alternative-text="Loading" size="medium"></lightning-spinner>
                    </template>

                    <template if:true={basicIDnVLoading}>
                        <div class="slds-box slds-box_small" style="height:100px;">
                            <lightning-spinner alternative-text="Loading" size="medium"></lightning-spinner>
                        </div>
                    </template>

                    <!-- Agent Landing -->
                    <template if:true={agentLandingButton}>
                        <p>{agentLandingDisplayText}</p>
                    </template>

                    <!-- Questions -->
                    <template if:false={agentLandingButton}>
                        <template if:true={resultData.questions}>

                            <template for:each={resultData.questions} for:item="q">

                                <div key={q.id} class="slds-m-bottom_small">

                                    <!-- Input -->
                                    <lightning-input
                                        key={q.id}
                                        type={q.type}
                                        label={q.text}
                                        variant="label-stacked"
                                        value={q.value}
                                        required
                                        maxlength={q.maxlength}
                                        onchange={handleValidations}
                                        autocomplete="off">
                                    </lightning-input>

                                    <!-- Autofill Checkbox -->
                                    <template if:true={q.autoFillCheckboxList}>
                                        <template for:each={q.autoFillCheckboxList} for:item="item">
                                            <lightning-input
                                                key={item.id}
                                                type="checkbox"
                                                label={item.label}
                                                onchange={handleCheckBoxOnChange}>
                                            </lightning-input>
                                        </template>
                                    </template>

                                </div>

                            </template>

                        </template>

                        <!-- Customer Table -->
                        <template if:true={isCustomersFound}>
                            <div
                                class="slds-scrollable_x slds-scrollable_y scroller-custom slds-box slds-box_xx-small"
                                style="height: 300px;"
                                id="modal-datatable-01">

                                <lightning-datatable
                                    key-field="id"
                                    data={accountsList}
                                    columns={columns}
                                    hide-checkbox-column
                                    column-widths-mode="auto"
                                    min-column-width="150"
                                    onrowaction={openCustomerLanding}
                                    aria-label="Datatable"
                                    aria-labelledby="modal-datatable-01">
                                </lightning-datatable>

                            </div>
                        </template>

                        <!-- Approve API -->
                        <template if:true={isApproveApi}>
                            <div class="slds-box slds-box_small">
                                <p aria-live="polite">
                                    <lightning-formatted-text
                                        class="slds-m-left_x-small"
                                        value="Approve API is called">
                                    </lightning-formatted-text>
                                </p>
                            </div>
                        </template>

                    </template>

                </div>

                <!-- Footer -->
                <footer class="slds-modal__footer">

                    <template if:false={agentLandingButton}>
                        <template if:true={resultData.questions}>

                            <!-- Unidentified Prospect Button -->
                            <template if:true={isDisplayUnidentifiedBtn}>
                                <lightning-button
                                    label={label.Unidentified_Prospect_Button}
                                    title={label.Unidentified_Prospect_Button}
                                    onclick={openworkflowtiav}
                                    data-id="mySidenavworkflow"
                                    icon-name="utility:user"
                                    variant="neutral"
                                    class="toleftalign slds-m-left_x-small">
                                </lightning-button>
                            </template>

                            <!-- Footer Controls -->
                            <div class="idnvFooter">

                                <lightning-combobox
                                    label="Call Dropped Reason"
                                    value={value}
                                    variant="label-hidden"
                                    placeholder="Close Customer Locate"
                                    options={options}
                                    onchange={handleCallDropdownChange}
                                    class="callDropReasonCombobox">
                                </lightning-combobox>

                                <template if:true={showBypassBtn}>
                                    <lightning-button
                                        variant="brand"
                                        label="ByPass"
                                        onclick={handleBypassButton}
                                        class="slds-button"
                                        disabled={bypassButtonDisabled}>
                                    </lightning-button>
                                </template>

                                <lightning-button
                                    variant="brand"
                                    label="Verify"
                                    onclick={handleSubmit}
                                    class="slds-button"
                                    disabled={checkValidity}>
                                </lightning-button>

                            </div>

                        </template>
                    </template>

                    <!-- Agent Landing Button -->
                    <template if:true={agentLandingButton}>
                        <lightning-button
                            variant="brand"
                            label={btnAgentLandingText}
                            onclick={handleReloadButtuon}
                            class="slds-button">
                        </lightning-button>
                    </template>

                </footer>

            </div>

            <!-- Backdrop -->
            <div class="slds-backdrop slds-backdrop_open"></div>

        </section>
    </template>

    <!-- Workflow Popup -->
    <div data-id="workflowPopup">
        <template if:true={showworkFlow}>

            <lightning-icon
                class="slds-p-around_x-small x-small1"
                data-id="x-small-button"
                icon-name="utility:close"
                onclick={closeworkflowNav}
                size="x-medium">
            </lightning-icon>

            <c-care_unidentifyworkflow
                account-info
                unidentifyinfo={unidentifyinfo}
                onclose={closemodalworkflow}
                ucontactid={ucontactid}
                idnvobj={idnvobj}>
            </c-care_unidentifyworkflow>

        </template>
    </div>

    <!-- Step-Up -->
    <template if:true={isStepUpApi}>
        <c-care_step-up-container
            onstepupvalidatesuccess={handleActivityResponse}
            ondecision={handleDecision}>
        </c-care_step-up-container>
    </template>

</template>
