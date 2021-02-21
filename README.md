 social_media_server
#
Full-Stack: Social media App A user can sign up , sign in, sign out . A user can create, edit, delete a post. A user can like or unlike other people posts and comment. A user can delete his/her profile . A user can also follow others or unfollow.

#
Built with Javascript, React,CSS, Bootstrap, Node, Express, MongoDB.

#
Project live :

http://determined-relation.surge.sh/

#
Youtub Video: https://youtu.be/HHYsmh-NdKI

#
Code Examples
#
Here is where I created postById() function to handle any post that need post Id to perform any task. I also imported their controllers:

# 
const Post = require('../models/post');
#
const formidable = require('formidable');
#
const fs = require('fs');
#
const _ = require('lodash');
#
exports.postById = (req, res, next, id) => {
#
    Post.findById(id)
    #
        .populate('postedBy', '_id name')
        #
        .populate('comments', 'text created')
        #
        .populate('comments.postedBy', '_id name')
        #
        .populate('postedBy', '_id name role')
        #
        .select('_id title body created likes comments photo')
        #
        .exec((err, post) => {
        #
            if (err || !post) {
            #
                return res.status(400).json({
                #
                    error: err
                    #
                });
                #
            }
            #
            req.post = post;
            #
            next();
            #
        });
        #
};

#
Here is where I created the routes for all of like unlike, comments, post routes, photos:

#
const express = require('express');
#
const {
    getPosts,
    createPost,
    postsByUser,
    postById,
    isPoster,
    updatePost,
    deletePost,
    photo,
    singlePost,
    like,
    unlike,
    comment,
    uncomment,
    updateComment
} = require('../controllers/post');
#
const { requireSignin } = require('../controllers/auth');
#
const { userById } = require('../controllers/user');
#
const { createPostValidator } = require('../validator');
#
const router = express.Router();
#
router.get('/posts', getPosts);
#
// like unlike
#
router.put('/post/like', requireSignin, like);
#
router.put('/post/unlike', requireSignin, unlike);
#
// comments
#
router.put('/post/comment', requireSignin, comment);
#
router.put('/post/uncomment', requireSignin, uncomment);
#
router.put('/post/updatecomment', requireSignin, updateComment);
#
// post routes
#
router.post('/post/new/:userId', requireSignin, createPost, createPostValidator);
#
router.get('/posts/by/:userId', requireSignin, postsByUser);
#
router.get('/post/:postId', singlePost);
#
router.put('/post/:postId', requireSignin, isPoster, updatePost);
#
router.delete('/post/:postId', requireSignin, isPoster, deletePost);
#
// photo
#
router.get('/post/photo/:postId', photo);
#
// any route containing :userId, our app will first execute userById()
#
router.param('userId', userById);
#
// any route containing :postId, our app will first execute postById()
#
router.param('postId', postById);
#
module.exports = router;
