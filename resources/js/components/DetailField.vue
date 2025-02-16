<template>
    <panel-item :field="field">
        {{ field.name }}
        <div slot="value" class="seo-meta-detail">
            <b v-if="!hasSeo">You need some SEO data</b>
            <button
                type="button"
                class="btn btn-primary btn-default"
                @click="showSeoPreviews = !showSeoPreviews"
                v-if="hasSeo"
            >{{ showSeoPreviews ? 'Hide' : 'Show' }} SEO previews
            </button>
            <div class="seo-meta-detail__previews" v-if="showSeoPreviews && hasSeo" v-for="(locale,index) in availableLocales" :key="index">
                <div class="seo-meta-detail__wrapper">
                    <div class="seo-meta-detail__wrapper__label">Google [{{ locale }}]</div>
                    <div class="seo-meta-detail__wrapper__item seo-meta-detail__google">
                        <div class="seo-meta-detail__google__title">{{ seoTitleFor(locale) }}</div>
                        <div
                            class="seo-meta-detail__google__url"
                        >{{
                                (field.url || field.hostname).replace(/:\/\//, ':||').replace(/(\/)/g, ' › ').replace(':||', '://')
                            }}
                        </div>
                        <div
                            class="seo-meta-detail__google__description"
                        >{{ field.value.description[locale] }}
                        </div>
                    </div>
                </div>
                <div class="seo-meta-detail__wrapper">
                    <div class="seo-meta-detail__wrapper__label">Facebook [{{ locale }}]</div>
                    <div class="seo-meta-detail__wrapper__item seo-meta-detail__facebook">
                        <div class="seo-meta-detail__facebook__image" v-if="field.image_url">
                            <img :src="field.image_url"/>
                        </div>
                        <div class="seo-meta-detail__facebook__info">
                            <div
                                class="seo-meta-detail__facebook__domain"
                            >{{ field.hostname.replace(/^https?:\/\//g, '') }}
                            </div>
                            <div class="seo-meta-detail__facebook__title">{{ seoTitleFor(locale) }}</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </panel-item>
</template>

<script>
export default {
    props: ["resource", "resourceName", "resourceId", "field"],
    data() {
        return {
            showSeoPreviews: false,
            availableLocales: this.field.available_locales,
        };
    },
    computed: {
        hasSeo() {
            const value = this.field.value;
            let hasTitleForAnyLocale = false;
            for (const locale in value.title) {
                if (value.title[locale] && value.title[locale].trim() !== ''){
                    hasTitleForAnyLocale = true;
                }
            }
            return hasTitleForAnyLocale;
        },
    },
    methods:{
        seoTitleFor(locale) {
            const field = this.field;
            const value = field.value;
            if (value && value.title && value.title[locale]) {
                if (field.title_format) {
                    return field.title_format.replace(":text", value.title[locale]);
                }
                return value.title[locale];
            }
            return null;
        }
    }
};
</script>

<style lang="scss" scoped>
.seo-meta-detail__previews {
    margin: 30px 0 0;
}

.seo-meta-detail__wrapper {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    margin: 0 0 15px 0;
}

.seo-meta-detail__wrapper__label {
    padding-right: 15px;
    font-weight: bold;
    width: 120px;
}

.seo-meta-detail__wrapper__item {
    max-width: 500px;
    flex-grow: 1;
}

.seo-meta-detail__google {
    border: 1px solid #ebebeb;
    background: #fff;
    padding: 15px;
    font-family: Arial, sans-serif;
    color: #545454;
    font-size: 14px;
    line-height: 1.57;
    word-wrap: break-word;

    &__title {
        font-size: 20px;
        line-height: 1.3;
        color: #1a0dab;
    }

    &__url {
        font-size: 16px;
        padding-top: 1px;
        line-height: 1.5;
        color: #006621;
    }
}

.seo-meta-detail__facebook {
    border: 1px solid #dadde1;
    background: #f2f3f5;
    font-family: Helvetica, Arial, sans-serif;
    color: #4b4f56;
    font-size: 14px;
    line-height: 1.57;
    word-wrap: break-word;

    &__image {
        height: 273px;
        overflow: hidden;

        img {
            width: 100%;
        }
    }

    &__info {
        padding: 10px 12px;
    }

    &__title {
        font-size: 16px;
        line-height: 20px;
        font-weight: bold;
        color: #1d2129;
        padding: 5px 0;
    }

    &__domain {
        color: #606770;
        font-size: 12px;
        line-height: 16px;
        text-transform: uppercase;
    }
}
</style>
