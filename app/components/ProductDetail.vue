<template>
    <Page>
        <StackLayout>
            <Image :src="tappedProduct.image" height="300"
                stretch="aspectFill" />
            <ScrollView>
                <StackLayout class="m-10">
                    <Label class="h2" :text="tappedProduct.name" />
                    <Label class="body" :text="tappedProduct.desc" />

                    <Label class="h3 m-y-20 pull-right text-primary"
                        :text="'Unit Price: $' + tappedProduct.price" />

                    <Label class="h3" text="Quantity:" />

                    <TextField v-model="textFieldValue" keyboardType="phone"
                        hint="Enter text..." /> 
                    <Label class="h3 m-y-30"
                        :text="'SubTotal: $' + textFieldValue * tappedProduct.price" />
                    <Button class="h2 -primary -rounded-lg" text="Add to Cart"
                        @tap="onButtonTap" />
                </StackLayout>
            </ScrollView>
        </StackLayout>
    </Page>
</template>

<script>
    export default {
        props: ["tappedProduct"],
        methods: {
            onButtonTap() {
                console.log("Button was pressed");
                this.tappedProduct.quantity = parseInt(this.textFieldValue) ||
                    0;
                this.$navigateBack();
            }
        },
        data() {
            return {
                textFieldValue: this.tappedProduct.quantity || ""
            };
        }
    };
</script>

<style>
</style>