<script setup>
import { reactive } from 'vue'
import { FormKitSchema } from '@formkit/vue'

const multiStepFormSchema = [
{
    $formkit: 'multi-step',
    beforeStepChange: '$log',
    allowIncomplete: false,
    children: [
      {
        $formkit: 'step',
        name: 'stepOne',
        children: [
          {
            $formkit: 'text',
            label: 'name',
            validation: 'required',
          },
        ],
      },
      {
        $formkit: 'step',
        if: '$showStepTwo',
        name: 'stepTwo',
        children: [
          {
            $formkit: 'text',
            label: 'favorie color',
            validation: 'required',
          },
        ]
      },
      {
        $formkit: 'step',
        name: 'stepThree',
        children: [
          {
            $formkit: 'text',
            label: 'favorite memory',
            validation: 'required',
          },
        ]
      },
    ],
  },
]

const log = console.log

const schemaData = reactive({
  showStepTwo: true,
  log
})
</script>

<template>
  <FormKitSchema
    :schema="multiStepFormSchema"
    :data="schemaData"
  />

  <FormKit
    v-slot="{ value }"
    type="form"
  >
    <!-- should not render as it is not inside a multi-step -->
    <FormKit type="step" />

    <FormKit
      type="multi-step"
      tab-style="progress"
      valid-step-icon="star"
    >
      <FormKit
        type="step"
        name="personalInfo"
        label="1. Info"
        valid-step-icon="bitcoin"
      >
        <FormKit
          type="text"
          label="My Name"
          prefix-icon="avatarMan"
          validation="required"
        />
        <FormKit
          type="tel"
          label="Phone"
          prefix-icon="telephone"
          validation="required"
        />
      </FormKit>
    
      <FormKit
        v-if="schemaData.showStepTwo"
        type="step"
        label="2. References"
        name="references"
        :before-step-change="({ currentStep, targetStep, delta }) => {
          if (delta > 0) {
            if (currentStep.value.supply === '1') {
              log('stops next')
              return false
            }
          }
        }"
      >
        <FormKit
          type="textarea"
          name="supply"
          label="Please supply 2 references"
          validation="required"
        />

        <template #stepNext="{ handlers }">
          <div
            class="custom-next"
            @click="handlers.next"
          >
            My Custom Step Next
          </div>
        </template>
        <template #stepPrevious="{ handlers }">
          <div
            class="custom-next"
            @click="handlers.previous"
          >
            My Custom Step Previous
          </div>
        </template>
      </FormKit>

      <FormKit
        type="step"
        name="Supplemental"
        label="3. Supplemental"
        previous-label="Go back"
        :previous-attrs="{
          'data-something': 'some data'
        }"
      >
        <FormKit
          type="text"
          label="Why do you want to work here?"
          validation="required"
        />
        <FormKit
          type="radio"
          label="How did you hear about us"
          validation="required"
          :options="[
            { label: 'Google', value: 'google' },
            { label: 'Facebook', value: 'facebook' },
            { label: 'Twitter', value: 'twitter' },
            { label: 'Friend', value: 'friend' },
          ]"
        />

        <template #stepNext>
          <FormKit type="submit" />
        </template>
      </FormKit>
    </FormKit>

    <button
      type="button"
      @click="schemaData.showStepTwo = !schemaData.showStepTwo"
    >
      {{ schemaData.showStepTwo ? 'Hide' : 'Show' }} Step Two
    </button>

    <pre>{{ value }}</pre>
  </FormKit>
</template>


<style scoped>
.custom-next {
  padding: 1em;
  background: #000;
  color: #fff;
  cursor: pointer;
  user-select: none;
}
</style>