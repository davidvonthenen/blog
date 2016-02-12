---
post_title: 'Test'
layout: post
published: true
---
Start writing here...

`    public long getElasticNodeId() {
        List<TaskInfo> taskList = getTaskList();

        //create a bitmask of all the node ids currently being used
        long bitmask = 0;
        for (TaskInfo info : taskList) {
            LOGGER.debug("getElasticNodeId - Task:");
            LOGGER.debug(info.toString());
            for (Variable var : info.getExecutor().getCommand().getEnvironment().getVariablesList()) {
                if (var.getName().equalsIgnoreCase(ExecutorEnvironmentalVariables.ELASTICSEARCH_NODE_ID)) {
                    bitmask |= 1 << Integer.parseInt(var.getValue()) - 1;
                    break;
                }
            }
        }
        LOGGER.debug("Bitmask: " + bitmask);

        //the find out which node ids are not being used
        long lNodeId = 0;
        for (int i = 0; i < 31; i++) {
            if ((bitmask & (1 << i)) == 0) {
                lNodeId = i + 1;
                LOGGER.debug("Found Free: " + lNodeId);
                break;
            }
        }

        return lNodeId;
    }`

Another Test
```
public long getElasticNodeId() {
    List<TaskInfo> taskList = getTaskList();

    //create a bitmask of all the node ids currently being used
    long bitmask = 0;
    for (TaskInfo info : taskList) {
        LOGGER.debug("getElasticNodeId - Task:");
        LOGGER.debug(info.toString());
        for (Variable var : info.getExecutor().getCommand().getEnvironment().getVariablesList()) {
            if (var.getName().equalsIgnoreCase(ExecutorEnvironmentalVariables.ELASTICSEARCH_NODE_ID)) {
                bitmask |= 1 << Integer.parseInt(var.getValue()) - 1;
                break;
            }
        }
    }
    LOGGER.debug("Bitmask: " + bitmask);

    //the find out which node ids are not being used
    long lNodeId = 0;
    for (int i = 0; i < 31; i++) {
        if ((bitmask & (1 << i)) == 0) {
            lNodeId = i + 1;
            LOGGER.debug("Found Free: " + lNodeId);
            break;
        }
    }

    return lNodeId;
}
```

Another Another Test

<pre>
public long getElasticNodeId() {
    List<TaskInfo> taskList = getTaskList();

    //create a bitmask of all the node ids currently being used
    long bitmask = 0;
    for (TaskInfo info : taskList) {
        LOGGER.debug("getElasticNodeId - Task:");
        LOGGER.debug(info.toString());
        for (Variable var : info.getExecutor().getCommand().getEnvironment().getVariablesList()) {
            if (var.getName().equalsIgnoreCase(ExecutorEnvironmentalVariables.ELASTICSEARCH_NODE_ID)) {
                bitmask |= 1 << Integer.parseInt(var.getValue()) - 1;
                break;
            }
        }
    }
    LOGGER.debug("Bitmask: " + bitmask);

    //the find out which node ids are not being used
    long lNodeId = 0;
    for (int i = 0; i < 31; i++) {
        if ((bitmask & (1 << i)) == 0) {
            lNodeId = i + 1;
            LOGGER.debug("Found Free: " + lNodeId);
            break;
        }
    }

    return lNodeId;
}
</pre>


Last test code:

<code>
public long getElasticNodeId() {
    List<TaskInfo> taskList = getTaskList();

    //create a bitmask of all the node ids currently being used
    long bitmask = 0;
    for (TaskInfo info : taskList) {
        LOGGER.debug("getElasticNodeId - Task:");
        LOGGER.debug(info.toString());
        for (Variable var : info.getExecutor().getCommand().getEnvironment().getVariablesList()) {
            if (var.getName().equalsIgnoreCase(ExecutorEnvironmentalVariables.ELASTICSEARCH_NODE_ID)) {
                bitmask |= 1 << Integer.parseInt(var.getValue()) - 1;
                break;
            }
        }
    }
    LOGGER.debug("Bitmask: " + bitmask);

    //the find out which node ids are not being used
    long lNodeId = 0;
    for (int i = 0; i < 31; i++) {
        if ((bitmask & (1 << i)) == 0) {
            lNodeId = i + 1;
            LOGGER.debug("Found Free: " + lNodeId);
            break;
        }
    }

    return lNodeId;
}
</code>
