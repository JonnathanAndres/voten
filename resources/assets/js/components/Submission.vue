<template>
	<transition name="el-fade-in-linear">
		<div class="submission-item submission-wrapper"
            @dblclick="doubleClicked"
		     v-show="!hidden"
		     :id="'submission' + list.id">
			<!-- side-voting -->
			<div class="side-voting desktop-only">
				<i class="v-icon v-up-fat side-vote-icon"
				   :class="upvoted ? 'go-primary animated bounceIn' : 'go-gray'"
				   @click="voteUp"></i>

				<div class="user-select vote-number">
					{{ points }}
				</div>

				<i class="v-icon v-down-fat side-vote-icon"
				   @click="voteDown"
				   :class="downvoted ? 'go-red animated bounceIn' : 'go-gray'"></i>
			</div>

			<article class="flex1"
			         v-bind:class="'box-typical profile-post ' + list.type">
				<!-- content -->
				<div class="profile-post-content">
					<text-submission v-if="list.type == 'text'"
					                 :submission="list"
					                 :nsfw="nsfw"
					                 :full="full"
					                 @bookmark="bookmark"
					                 :url="'/c/' + list.channel_name + '/' + list.slug"
					                 :comments="list.comments_number"
					                 :bookmarked="bookmarked"
					                 @report="report"
					                 @hide="hide"
					                 @nsfw="markAsNSFW"
					                 @sfw="markAsSFW"
					                 @destroy="destroy"
					                 @approve="approve"
					                 @disapprove="disapprove"
					                 @removethumbnail="removeThumbnail"
					                 :upvoted="upvoted"
					                 :downvoted="downvoted"
					                 @upvote="voteUp"
					                 @downvote="voteDown"
					                 :points="points"></text-submission>

					<img-submission v-if="list.type == 'img'"
					                :submission="list"
					                :nsfw="nsfw"
					                :full="full"
					                @zoom="showPhotoViewer"
					                @bookmark="bookmark"
					                :url="'/c/' + list.channel_name + '/' + list.slug"
					                :comments="list.comments_number"
					                :bookmarked="bookmarked"
					                @report="report"
					                @hide="hide"
					                @nsfw="markAsNSFW"
					                @sfw="markAsSFW"
					                @destroy="destroy"
					                @approve="approve"
					                @disapprove="disapprove"
					                @removethumbnail="removeThumbnail"
					                :upvoted="upvoted"
					                :downvoted="downvoted"
					                @upvote="voteUp"
					                @downvote="voteDown"
					                :points="points"></img-submission>

					<gif-submission v-if="list.type == 'gif'"
					                :submission="list"
					                :nsfw="nsfw"
					                :full="full"
					                @bookmark="bookmark"
					                @play-gif="showGifPlayer"
					                :url="'/c/' + list.channel_name + '/' + list.slug"
					                :comments="list.comments_number"
					                :bookmarked="bookmarked"
					                @report="report"
					                @hide="hide"
					                @nsfw="markAsNSFW"
					                @sfw="markAsSFW"
					                @destroy="destroy"
					                @approve="approve"
					                @disapprove="disapprove"
					                @removethumbnail="removeThumbnail"
					                :upvoted="upvoted"
					                :downvoted="downvoted"
					                @upvote="voteUp"
					                @downvote="voteDown"
					                :points="points"></gif-submission>

					<link-submission v-if="list.type == 'link'"
					                 :submission="list"
					                 :nsfw="nsfw"
					                 :full="full"
					                 @embed="showEmbed"
					                 @bookmark="bookmark"
					                 :url="'/c/' + list.channel_name + '/' + list.slug"
					                 :comments="list.comments_number"
					                 :bookmarked="bookmarked"
					                 @report="report"
					                 @hide="hide"
					                 @nsfw="markAsNSFW"
					                 @sfw="markAsSFW"
					                 @destroy="destroy"
					                 @approve="approve"
					                 @disapprove="disapprove"
					                 @removethumbnail="removeThumbnail"
					                 :upvoted="upvoted"
					                 :downvoted="downvoted"
					                 @upvote="voteUp"
					                 @downvote="voteDown"
					                 :points="points"></link-submission>
				</div>
			</article>
		</div>
	</transition>
</template>

<script>
import TextSubmission from '../components/submission/TextSubmission.vue';
import LinkSubmission from '../components/submission/LinkSubmission.vue';
import ImgSubmission from '../components/submission/ImgSubmission.vue';
import GifSubmission from '../components/submission/GifSubmission.vue';
import Helpers from '../mixins/Helpers';

export default {
    props: ['list', 'full'],

    mixins: [Helpers],

    components: {
        TextSubmission,
        LinkSubmission,
        ImgSubmission,
        GifSubmission
    },

    data() {
        return {
            hidden: false,
            sendingQuickComment: false,
            quickComment: '',
            embedViewer: false,
            gifPlayer: false
        };
    },

    computed: {
        upvoted: {
            get() {
                return Store.state.submissions.upVotes.indexOf(this.list.id) !== -1 ? true : false;
            },

            set() {
                if (this.currentVote === 'upvote') {
                    this.list.upvotes--;
                    let index = Store.state.submissions.upVotes.indexOf(this.list.id);
                    Store.state.submissions.upVotes.splice(index, 1);

                    return;
                }

                if (this.currentVote === 'downvote') {
                    this.list.downvotes--;
                    let index = Store.state.submissions.downVotes.indexOf(this.list.id);
                    Store.state.submissions.downVotes.splice(index, 1);
                }

                this.list.upvotes++;
                Store.state.submissions.upVotes.push(this.list.id);
            }
        },

        downvoted: {
            get() {
                return Store.state.submissions.downVotes.indexOf(this.list.id) !== -1 ? true : false;
            },

            set() {
                if (this.currentVote === 'downvote') {
                    this.list.downvotes--;
                    let index = Store.state.submissions.downVotes.indexOf(this.list.id);
                    Store.state.submissions.downVotes.splice(index, 1);

                    return;
                }

                if (this.currentVote === 'upvote') {
                    this.list.upvotes--;
                    let index = Store.state.submissions.upVotes.indexOf(this.list.id);
                    Store.state.submissions.upVotes.splice(index, 1);
                }

                this.list.downvotes++;
                Store.state.submissions.downVotes.push(this.list.id);
            }
        },

        bookmarked: {
            get() {
                return Store.state.bookmarks.submissions.indexOf(this.list.id) !== -1 ? true : false;
            },

            set() {
                if (Store.state.bookmarks.submissions.indexOf(this.list.id) !== -1) {
                    let index = Store.state.bookmarks.submissions.indexOf(this.list.id);
                    Store.state.bookmarks.submissions.splice(index, 1);

                    return;
                }

                Store.state.bookmarks.submissions.push(this.list.id);
            }
        },

        points() {
            let total = this.list.upvotes - this.list.downvotes;

            if (total < 0) return 0;

            return total;
        },

        /**
         * Does the auth user own the submission
         *
         * @return Boolean
         */
        owns() {
            return auth.id == this.list.owner.id;
        },

        /**
         * Whether or not user wants to see NSFW content's image
         *
         * (Hint: The base idea is that we don't display NSFW content)
         * If the user wants to see NSFW media then return false, like it's not NSFW at all
         * Otherwise return true which means the media must not be displayed.
         * (false: the media will be displayed)
         *
         * @return boolean
         */
        nsfw() {
            return this.list.nsfw && !auth.nsfwMedia;
        },

        /**
         * The current vote type. It's being used to optimize the voing request on the server-side.
         *
         * @return mixed
         */
        currentVote() {
            return this.upvoted ? 'upvote' : this.downvoted ? 'downvote' : null;
        },

        date() {
            return moment(this.list.created_at)
                .utc(moment().format('Z'))
                .fromNow();
        }
    },

    methods: {
        doubleClicked() {
            if (this.isGuest) return;
            
            if (!this.currentVote) {
                this.voteUp();
            }
        },

        removeThumbnail() {
            this.list.data.thumbnail = null;
            this.list.data.img = null;

            axios.post('/remove-thumbnail', { id: this.list.id });
        },

        /**
         * marks the submission as NSFW (not safe for work)
         *
         * @return void
         */
        markAsNSFW() {
            this.list.nsfw = true;

            axios
                .post('/mark-submission-nsfw', {
                    id: this.list.id
                })
                .catch(() => {
                    this.list.nsfw = false;
                });
        },

        /**
         * marks the submission as NSFW (not safe for work)
         *
         * @return void
         */
        markAsSFW() {
            this.list.nsfw = false;

            axios
                .post('/mark-submission-sfw', {
                    id: this.list.id
                })
                .catch(() => {
                    this.list.nsfw = true;
                });
        },

        /**
         * hide(block) submission
         *
         * @return void
         */
        hide() {
            if (this.isGuest) {
                this.mustBeLogin();
                return;
            }

            this.hidden = true;

            axios
                .post('/hide-submission', {
                    submission_id: this.list.id
                })
                .catch(() => {
                    this.hidden = false;
                });
        },

        /**
         * Deletes the submission. Only the owner is allowed to make such decision.
         *
         * @return void
         */
        destroy() {
            axios.post('/destroy-submission', { id: this.list.id });

            if (this.full) {
                this.$router.push('/');
            } else {
                this.hidden = true;
            }
        },

        /**
         * Approves the submission. Only the moderators of channel are allowed to do this.
         *
         * @return void
         */
        approve() {
            this.list.approved_at = this.now();

            axios
                .post('/approve-submission', {
                    submission_id: this.list.id
                })
                .catch(() => {
                    this.list.approved_at = null;
                });
        },

        /**
         * Disapproves the submission. Only the moderators of channel are allowed to do this.
         *
         * @return void
         */
        disapprove() {
            this.hidden = true;

            axios.post('/disapprove-submission', { submission_id: this.list.id }).catch(() => (this.hidden = false));
        },

        /**
         *  Report submission
         *
         *  @return void
         */
        report() {
            if (this.isGuest) {
                this.mustBeLogin();
                return;
            }

            Store.modals.reportSubmission.show = true;
            Store.modals.reportSubmission.submission = this.list;
        },

        voteUp: _.debounce(
            function() {
                if (this.isGuest) {
                    this.mustBeLogin();
                    return;
                }

                axios.post('/upvote-submission', {
                    submission_id: this.list.id,
                    previous_vote: this.currentVote
                });

                if (this.currentVote === 'upvote') {
                    this.upvoted = false;
                    return;
                } else if (this.currentVote === 'downvote') {
                    this.downvoted = false;
                }

                this.upvoted = true;
            },
            200,
            { leading: true, trailing: false }
        ),

        voteDown: _.debounce(
            function() {
                if (this.isGuest) {
                    this.mustBeLogin();
                    return;
                }

                axios.post('/downvote-submission', {
                    submission_id: this.list.id,
                    previous_vote: this.currentVote
                });

                if (this.currentVote === 'downvote') {
                    this.downvoted = false;
                    return;
                } else if (this.currentVote === 'upvote') {
                    this.upvoted = false;
                }

                this.downvoted = true;
            },
            200,
            { leading: true, trailing: false }
        ),

        bookmark: _.debounce(
            function() {
                if (this.isGuest) {
                    this.mustBeLogin();
                    return;
                }

                this.bookmarked = !this.bookmarked;

                axios
                    .post('/bookmark-submission', {
                        id: this.list.id
                    })
                    .catch(() => {
                        this.bookmarked = !this.bookmarked;
                    });
            },
            200,
            { leading: true, trailing: false }
        ),

        showPhotoViewer(image) {
            Store.modals.photoViewer.image = image;
            Store.modals.photoViewer.submission = this.list;
            Store.modals.photoViewer.show = true;
        },

        showEmbed() {
            Store.modals.embedViewer.submission = this.list;
            Store.modals.embedViewer.show = true;
        },

        showGifPlayer(gif) {
            Store.modals.gifPlayer.gif = gif;
            Store.modals.gifPlayer.submission = this.list;
            Store.modals.gifPlayer.show = true;
        }
    }
};
</script>
