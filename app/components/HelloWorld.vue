<template>
    <Page>
        <ActionBar title="Home" />

        <StackLayout>
            <BottomNavigation height="400px">
                <TabStrip>
                    <TabStripItem>
                        <Label text="Ladies"></Label>
                        <Image src="res://home"></Image>
                    </TabStripItem>
                    <TabStripItem>
                        <Label text="Gents"></Label>
                        <Image src="res://settings"></Image>
                    </TabStripItem>
                    <TabStripItem>
                        <Label text="My Cart"></Label>
                        <Image src="res://search"></Image>
                    </TabStripItem>
                    <TabStripItem>
                        <Label text="Figures"></Label>
                        <Image src="res://search"></Image>
                    </TabStripItem>
                </TabStrip>
                <TabContentItem>
                    <ListView for="product in ladies" @itemTap="onItemTap">
                        <v-template>
                            <StackLayout orientation="vertical" height="350">
                                <Image :src="product.image" height="300"
                                    stretch="aspectFill" />
                                <Label :text="product.name" class="h2" />
                            </StackLayout>
                        </v-template>
                    </ListView>
                </TabContentItem>
                <TabContentItem>
                    <ListView for="product in gents" @itemTap="onItemTap">
                        <v-template>
                            <StackLayout orientation="vertical" height="350">
                                <Image :src="product.image" height="300"
                                    stretch="aspectFill" />
                                <Label :text="product.name" class="h2" />
                            </StackLayout>
                        </v-template>
                    </ListView>
                </TabContentItem>
                <TabContentItem>
                    <StackLayout class="m-10">
                        <Label class="h2" text="My Shopping Cart" />

                        <ListView for="product in inCart" @itemTap="onItemTap"
                            height="300">
                            <v-template>
                                <FlexboxLayout flexDirection="row">
                                    <Image :src="product.image"
                                        class="thumb img-circle" />
                                    <Label
                                        :text="product.name + ' x ' + product.quantity"
                                        class="t-12" style="width: 60%" />
                                </FlexboxLayout>
                            </v-template>
                        </ListView>

                        <Label :text="'Total: $' + total" class="h2 m-y-20" />
                        <Button class="h2 -primary -rounded-lg"
                            text="Place Order" @tap="onButtonTap" />
                        <Label class="m-y-30"
                            :text="'Bought: ' + figures.length" />
                    </StackLayout>
                </TabContentItem>
                <TabContentItem>
                    <GridLayout rows="*" height="1000px">
                        <RadCartesianChart row="0" style="font-size: 12;">
                            <BarSeries v-tkCartesianSeries :items="figures"
                                categoryProperty="name"
                                valueProperty="quantity" />
                            <CategoricalAxis v-tkCartesianHorizontalAxis />
                            <LinearAxis v-tkCartesianVerticalAxis />
                        </RadCartesianChart>
                    </GridLayout>
                </TabContentItem>
            </BottomNavigation>
        </StackLayout>

    </Page>
</template>
<script>
    import Vue from "nativescript-vue";
    import RadCartesianChart from "nativescript-ui-chart/vue";
    Vue.use(RadCartesianChart);
    import ProductDetail from "./ProductDetail";
    export default {
        methods: {
            onItemTap: function(args) {
                console.log("Item with index: " + args.index + " tapped");
                console.log("Product tapped: " + args.item.name);
                this.$navigateTo(ProductDetail, {
                    transition: {},
                    props: {
                        tappedProduct: args.item
                    }
                });
            },
            onButtonTap: async function() {
                var result = await confirm({
                    title: "Confirm to place order?",
                    message: "Sending to httpbin.org",
                    okButtonText: "Yes",
                    cancelButtonText: "Cancel"
                });
                if (result) {
                    try {
                        var response = await fetch(
                            "https://httpbin.org/post", {
                                method: "POST",
                                headers: {
                                    "Content-Type": "application/json"
                                },
                                body: JSON.stringify(this.inCart)
                            });
                        if (response.ok) {
                            var data = await response.json();
                            this.figures = data.json;
                        } else {
                            console.log(response.status);
                        }
                    } catch (error) {
                        console.log(error);
                    }
                }
            }
        },
        mounted() {
            this.ladies = this.products.filter(function(p) {
                return p.department == "Ladies";
            });
            this.gents = this.products.filter(function(p) {
                return p.department == "Gents";
            });
        },
        computed: {
            inCart: function() {
                return this.products.filter(function(p) {
                    return p.quantity > 0;
                });
            },
            total: function() {
                var sum = 0;
                this.products.forEach(function(p) {
                    sum += p.quantity * p.price;
                });
                return sum;
            }
        },
        data() {
            return {
                figures: [],
                ladies: [],
                gents: [],
                products: [{
                        id: "1",
                        department: "Gents",
                        name: "Denim Men's Shirts",
                        desc: "Passion, dedication, hard work and creativity",
                        image: "https://cdn.stocksnap.io/img-thumbs/960w/GAVVVYCGXC.jpg",
                        price: 450,
                        quantity: 0
                    },
                    {
                        id: "2",
                        department: "Ladies",
                        name: "RUN Hoodies",
                        desc: "Top-quality, instant-favorite sweatshirt",
                        image: "https://cdn.stocksnap.io/img-thumbs/960w/UHAQDIYT6X.jpg",
                        price: 600,
                        quantity: 0
                    },
                    {
                        id: "3",
                        department: "Gents",
                        name: "Men's Lightweight Coat",
                        desc: "Signature Buck appliqué",
                        image: "https://cdn.stocksnap.io/img-thumbs/960w/PJY3Y7010M.jpg",
                        price: 2500,
                        quantity: 0
                    },
                    {
                        id: "4",
                        department: "Ladies",
                        name: "Story Story Tee",
                        desc: "Ultra soft T-shirt",
                        image: "https://cdn.stocksnap.io/img-thumbs/960w/GCJ7VU3PZ0.jpg",
                        price: 340,
                        quantity: 0
                    }
                ]
            };
        }
    };
</script>

<style scoped>
    .home-panel {
        vertical-align: center;
        font-size: 20;
        margin: 15;
    }

    .description-label {
        margin-bottom: 15;
    }
</style>