<template if:true={modalisopen}>

    <!-- ✅ Close Confirmation Dialog -->
    <c-care_confirmation-dialog
        title={Care_CloseReason.Pop_Up_Header}
        message={Care_CloseReason.Pop_Up_Message}
        cancel-label={Care_CloseReason.CancelLabel}
        confirm-label={Care_CloseReason.ConfirmLabel}
        visible={showCloseIdnvConfirmation}
        name="confirmModal"
        onclosemodalclick={handleConfirmDialog}>
    </c-care_confirmation-dialog>

    <!-- ✅ Main Modal -->
    <template if:false={isstepUpApi}>
        <section
            role="dialog"
            tabindex="-1"
            aria-labelledby="modal-heading"
            aria-describedby="idnv-announcement"
            class="slds-modal slds-fade-in-open">

            <div class="slds-modal__container">

                <!-- ✅ Screen reader announcement (ONLY this, not full content) -->
                <div id="idnv-announcement" class="slds-assistive-text">
                    Identity Verification Required
                </div>

                <!-- ✅ Header -->
                <header class="slds-modal__header">
                    <h2 id="modal-heading" class="slds-text-heading_medium">
                        Locate Customer
                    </h2>
                </header>

                <!-- ✅ Body -->
                <div class="slds-modal__content slds-p-around_medium">

                    <!-- ✅ Spinner -->
                    <template if:true={submitSpinner}>
                        <lightning-spinner alternative-text="Loading" size="medium"></lightning-spinner>
                    </template>

                    <template if:true={basicIDnVLoading}>
                        <lightning-spinner alternative-text="Loading" size="medium"></lightning-spinner>
                    </template>

                    <!-- ✅ Agent Landing Message -->
                    <template if:true={agentLandingButton}>
                        <p>{agentLandingDisplayText}</p>
                    </template>

                    <!-- ✅ Questions -->
                    <template if:false={agentLandingButton}>
                        <template if:true={resultData.questions}>

                            <template for:each={resultData.questions} for:item="q">

                                <div key={q.id} class="slds-m-bottom_small">

                                    <!-- ✅ Label (ONLY ONE source of truth) -->
                                    <label class="slds-form-element__label" for={q.id}>
                                        {q.text} <span class="slds-text-color_error">*</span>
                                    </label>

                                    <!-- ✅ Input (NO extra aria clutter) -->
                                    <lightning-input
                                        id={q.id}
                                        name={q.id}
                                        type={q.type}
                                        value={q.value}
                                        required
                                        maxlength={q.maxlength}
                                        onchange={handleValidations}
                                        autocomplete="off"
                                        variant="label-hidden">
                                    </lightning-input>

                                    <!-- ✅ Optional Autofill Checkbox -->
                                    <template if:true={q.autoFillCheckboxList}>
                                        <template for:each={q.autoFillCheckboxList} for:item="item">
                                            <lightning-input
                                                key={item.label}
                                                type="checkbox"
                                                label={item.label}
                                                onchange={handleCheckBoxOnChange}>
                                            </lightning-input>
                                        </template>
                                    </template>

                                </div>

                            </template>

                        </template>
                    </template>

                    <!-- ✅ Customer Table -->
                    <template if:true={isCustomersFound}>
                        <div class="slds-box slds-scrollable_y" style="height:300px;">
                            <lightning-datatable
                                key-field="id"
                                data={accountsList}
                                columns={columns}
                                hide-checkbox-column
                                onrowaction={openCustomerLanding}>
                            </lightning-datatable>
                        </div>
                    </template>

                    <!-- ✅ Approve API Message (ONLY place where aria-live is OK) -->
                    <template if:true={isApproveApi}>
                        <p aria-live="polite">
                            Approve API is called
                        </p>
                    </template>

                </div>

                <!-- ✅ Footer -->
                <footer class="slds-modal__footer">

                    <template if:false={agentLandingButton}>

                        <!-- Dropdown -->
                        <lightning-combobox
                            label="Call Dropped Reason"
                            value={value}
                            placeholder="Close Customer Locate"
                            options={options}
                            onchange={handleCallDropdownChange}>
                        </lightning-combobox>

                        <!-- Buttons -->
                        <template if:true={showBypassBtn}>
                            <lightning-button
                                variant="brand"
                                label="ByPass"
                                onclick={handleBypassButton}
                                disabled={bypassButtonDisabled}>
                            </lightning-button>
                        </template>

                        <lightning-button
                            variant="brand"
                            label="Verify"
                            onclick={handleSubmit}
                            disabled={checkValidity}>
                        </lightning-button>

                    </template>

                    <!-- Agent Landing Button -->
                    <template if:true={agentLandingButton}>
                        <lightning-button
                            variant="brand"
                            label={btnAgentLandingText}
                            onclick={handleReloadButtuon}>
                        </lightning-button>
                    </template>

                </footer>

            </div>

            <!-- Backdrop -->
            <div class="slds-backdrop slds-backdrop_open"></div>

        </section>
    </template>

    <!-- ✅ Step-Up API -->
    <template if:true={isStepUpApi}>
        <c-care_step-up-container
            onstepupvalidatesuccess={handleActivityResponse}
            ondecision={handleDecision}>
        </c-care_step-up-container>
    </template>

</template>
