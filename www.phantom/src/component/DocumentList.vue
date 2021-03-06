<template>
    <div>
        <PageTitleWithActions :title="plural | t" :actions="actions" />
        <Loader v-if="!documents" text="Loading documents from cache" />
        <div v-if="hasDrafts" class="urgent">
            <div class="urgent-title">{{ 'unpublished_drafts' | t }}</div>
            <div>{{ 'unpublished_drafts_help' | t }}</div>
        </div>
        <table v-if="documents">
            <thead>
            <tr>
                <th>{{ 'title' | t }}</th>
                <th>{{ 'status' | t }}</th>
                <th>{{ 'updated' | t }}</th>
                <th>{{ 'created' | t }}</th>
                <th></th>
            </tr>
            </thead>
            <tbody>
            <tr v-if="documents && !documents.length">
                <td colspan="5">{{ 'no_' + plural | t}}</td>
            </tr>
            <tr v-for="document in documents">
                <td>
                    <div class="column">{{ 'title' | t }}</div>
                    <a v-if="document.state === 'published'" :href="($root.$data.domain + '/#' + urlPrefix + '/' + document.file) | safeURL" target="_blank">{{ document.title }}</a>
                    <span v-if="document.state !== 'published'">{{ document.title }}</span>
                </td>
                <td>
                    <div class="column">{{ 'status' | t }}</div>
                    <div class="document-state" :class="document.state">{{ document.state }}</div>
                </td>
                <td>
                    <div class="column">{{ 'updated' | t }}</div>
                    {{ document.modified | timeAgo }}
                </td>
                <td>
                    <div class="column">{{ 'created' | t }}</div>
                    {{ document.created | timeAgo }}
                </td>
                <td><router-link :to="'/app/' + single.toLowerCase() + '/' + document.file" class="button">{{ 'edit' | t }}</router-link></td>
            </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    import PageTitleWithActions from "@/component/PageTitleWithActions";
    import Loader from "@/component/Loader";
    import api from "@/service/safe/api";
    import formatter from "@/service/markdown/formatter";
    import canonical from '@/service/markdown/canonical';

    export default {
        name: 'document-list',
        components: {
            PageTitleWithActions,
            Loader
        },
        props: {
            single: String,
            plural: String,
            urlPrefix: String
        },
        data: function() {
            return {
                documents: false,
                hasDrafts: false,
                actions: [],
                theme: false
            }
        },
        methods: {
            createDocument: function() {
                let document = {
                    file: Math.random().toString(36).substr(2, 10),
                    state: "local-draft",
                    data: false,
                    map: false,
                    created: (new Date).toISOString(),
                    modified: (new Date).toISOString()
                };

                api.addGenericDocument(this.$root.$data.domain, document, this.plural.toLowerCase());
                this.$router.push("/app/" + this.single.toLowerCase() + "/" + document.file);
            },
            publishDrafts: function() {
                api.getTheme(this.$root.$data.domain).then(theme => {
                    theme.getComputedTemplate(this.$root.$data.domain).then(template => {
                        api.updateFile(template, this.$root.$data.domain, "index.html", true).then(response => {
                            this.resetActions();
                            this.load();
                        });
                    });
                });
            },
            load: function() {
                api.getGenericDocuments(this.$root.$data.domain, this.plural.toLowerCase()).then(documents => {
                    for (let i = 0; i < documents.length; i++) {
                        this.hasDrafts = this.hasDrafts || documents[i].state === "draft";
                        documents[i].title = formatter.getTitle((!documents[i].data || documents[i].data === "") ? formatter.getDefaultMarkdown(this.single.toLowerCase(), this.$options.filters.t) : canonical.getMarkdownFromHTML(documents[i].data));
                    }

                    if (this.hasDrafts) {
                        this.actions.push({
                            text: this.$options.filters.t('publish_drafts'),
                            callback: this.publishDrafts
                        });
                    }

                    this.documents = documents;
                });
            },
            resetActions: function() {
                this.hasDrafts = false;
                this.actions = [{ text: this.$options.filters.t("create_" + this.single), callback: this.createDocument }];
            }
        },
        mounted() {
            this.resetActions();
            this.load();
        }
    }
</script>

<style scoped lang="scss">
    .urgent {
        margin-top: 30px;
        padding: 20px;
        background-color: #264c74;
        color: #fff;
        border-radius: 5px;

        .urgent-title {
            padding-bottom: 5px;
            font-weight: bold;
        }
    }

    .document-state {
        display: inline-block;
        vertical-align: top;
        padding: 2px 8px;
        color: #fff;
        font-weight: bold;
        font-size: 11px;
        text-transform: uppercase;
        border-radius: 4px;

        &.local-draft {
            background-color: #b35230;
        }

        &.draft {
            background-color: #b3a01d;
        }

        &.published {
            background-color: #50b31d;
        }
    }
</style>