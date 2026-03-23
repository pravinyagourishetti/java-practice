<template for:each={resultData.questions} for:item="q">

    <div key={q.id} class="slds-m-bottom_small">

        <!-- ✅ Input with proper label -->
        <lightning-input
            key={q.id}
            type={q.type}
            label={q.text}
            value={q.value}
            required
            maxlength={q.maxlength}
            onchange={handleValidations}
            autocomplete="off">
        </lightning-input>

        <!-- ✅ Checkbox -->
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
