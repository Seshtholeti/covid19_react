var https = require('https');

exports.handler = function (contact, context, callback) {
    console.log('event',contact,context,callback);
    // var params = contact.Details.Parameters;
    // params.callback = callback;
    var params = {
      callback:'+919949921498',
      post_data:{},
      post_options:{}
    };
    params.callback = '+919949921498';
    getRequestData(params);
    getPostOptions(params);
    postData(params);
};

var postData = function (params) {

    var post_request = https.request(params.post_options, function (res) {
        var body = '';
        console.log('api',res);
        res.on('data', chunk => body += chunk);
        res.on('end', () => console.log(JSON.parse(body))); 
        res.on('error', e => context.fail('error:' + e.message));
    });

    post_request.write(params.post_data);
    post_request.end();
};

var getPostOptions = function(params){
    params.post_options = {
        host: process.env.SERVICENOW_HOST,
        port: '443',
        path: '/api/now/table/incident',
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Accept': 'application/json',
            'Authorization': 'Basic ' + Buffer.from(process.env.SERVICENOW_USERNAME + ":" + process.env.SERVICENOW_PASSWORD).toString('base64'),
            'Content-Length': Buffer.byteLength(params.post_data)
        }
    };
};

var getRequestData = function (params) {
    var payload = {
      short_description: 'paramsDescription',
      created_by: 'paramsUsername',
      caller_id: '+919949921498',
    };
    params.post_data = JSON.stringify(payload);
};
