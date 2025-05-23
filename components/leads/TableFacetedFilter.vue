<script setup lang="ts">
import type { Column } from '@tanstack/vue-table'
import type { Component } from 'vue'
import { cn } from '@/lib/utils'
import { Check, PlusCircle } from 'lucide-vue-next'
import type { Tables } from '~/types/supabase'

interface DataTableFacetedFilter {
  column?: Column<Tables<'leads'>, any>
  title?: string
  options: {
    label: string
    value: string
    icon?: Component
  }[]
  disabled?: boolean
}

const props = defineProps<DataTableFacetedFilter>()

const facets = computed(() => props.column?.getFacetedUniqueValues())
const selectedValues = computed(() => new Set(props.column?.getFilterValue() as string[]))
</script>

<template>
  <Popover>
    <PopoverTrigger as-child :disabled="disabled">
      <Button variant="outline" size="sm" class="h-8 border-dashed" :disabled="disabled">
        <PlusCircle class="size-4" />
        {{ title }}
        <template v-if="selectedValues.size > 0">
          <div class="mx-2 h-4 border border-dashed" />
          <Badge variant="secondary" class="rounded-sm px-1 font-normal lg:hidden">
            {{ selectedValues.size }}
          </Badge>
          <div class="hidden space-x-1 lg:flex">
            <Badge v-if="selectedValues.size > 2" variant="secondary" class="rounded-sm px-1 font-normal">
              {{ selectedValues.size }} selected
            </Badge>

            <template v-else>
              <Badge v-for="option in options
                .filter((option) => selectedValues.has(option.value))" :key="option.value" variant="secondary"
                class="rounded-sm px-1 font-normal">
                {{ option.label }}
              </Badge>
            </template>
          </div>
        </template>
      </Button>
    </PopoverTrigger>
    <PopoverContent class="w-[250px] p-0" align="start">
      <Command>
        <CommandInput :placeholder="title" />
        <CommandList>
          <CommandEmpty>No results found.</CommandEmpty>
          <CommandGroup>
            <CommandItem v-for="option in options" :key="option.value" :value="option" @select="() => {
              const isSelected = selectedValues.has(option.value)
              if (isSelected) {
                selectedValues.delete(option.value)
              }
              else {
                selectedValues.add(option.value)
              }
              const filterValues = Array.from(selectedValues)
              column?.setFilterValue(
                filterValues.length ? filterValues : undefined,
              )
            }" class="px-2 py-1.5">
              <div :class="cn(
                'mr-2 flex size-4 items-center justify-center rounded-sm border border-primary',
                selectedValues.has(option.value)
                  ? 'bg-primary text-primary-foreground'
                  : 'opacity-50 [&_svg]:invisible',
              )">
                <Check :class="cn('size-3.5 text-primary-foreground')" />
              </div>
              <component :is="option.icon" v-if="option.icon" class="size-4 text-muted-foreground" />
              <span>{{ option.label }}</span>
              <span v-if="facets?.get(option.value)"
                class="ml-auto flex size-4 items-center justify-center font-mono text-xs">
                {{ facets.get(option.value) }}
              </span>
            </CommandItem>
          </CommandGroup>

          <template v-if="selectedValues.size > 0">
            <CommandSeparator />
            <CommandGroup>
              <CommandItem :value="{ label: 'Clear filters' }" class="justify-center text-center"
                @select="column?.setFilterValue(undefined)">
                Clear filters
              </CommandItem>
            </CommandGroup>
          </template>
        </CommandList>
      </Command>
    </PopoverContent>
  </Popover>
</template>